---
layout: post
title:  "Small is Beautiful"
date:   2017-03-17 16:54:39 +0100
categories: refactoring clean-code principles
comments: true
---

Does size matter? It does and the conclusion is pretty clear: smaller is better. Small is beautiful.

<div>
<iframe src="https://www.youtube.com/embed/B3b4tremI5o?ecver=2" width="500" height="275" allowfullscreen></iframe>
</div>

As a developer, the best way to keep the code clean and maintainable is by keeping it small: instead of having big methods, break them into smaller ones - some end up being classes, others just methods, but all end up being small parts and very clear in what they do.


### Getting Small

A while ago I was helping this friend improving his code.
He needed to write this piece of code that would fetch certain types of content from a remote server.

When I first looked at it, it looked something as you see below.

Things got a bit too big, making it hard to do _anything_ with it.

{% highlight java %}

public class ImporterBasicUtils {
    private static Log LOGGER = LogFactoryUtil.getLog(ImporterBasicUtils.class);

    public static boolean checkServerIsOnline(ServiceContext serviceContext) {
        boolean online = false;
        int connectionTimeout = 0;

        try {
            connectionTimeout = Integer.parseInt((String) serviceContext.getAttribute("connectTimeout"));
        } catch (NumberFormatException nfe) {
            LOGGER.error("Connection timeout setting not a number? " + serviceContext.getAttribute("connectTimeout"));
        }

        try {
            LOGGER.debug("Checking source availability");
            URL sourceSystem = new URL((String) serviceContext.getAttribute("sourceSystem"));

            HttpURLConnection huc = (HttpURLConnection) sourceSystem.openConnection();
            huc.setConnectTimeout(connectionTimeout * 1000);
            huc.setRequestMethod("GET");
            huc.connect();

            if (huc.getResponseCode() != 200) {
                online = false;
                LOGGER.error("No 200 response from source system.");
            } else {
                LOGGER.debug("Successfully found source, response code 200");
                online = true;
            }
        } catch (MalformedURLException murle) {
            LOGGER.debug("MalformedURLException with: " + (String) serviceContext.getAttribute("sourceSystem"), murle);
            online = false;
        } catch (ProtocolException e) {
            LOGGER.debug("Something's wrong with the protocol for the sourceSystem connection", e);
            online = false;
        } catch (IOException e) {
            LOGGER.debug("IOException from the sourceSystem: ", e);
            online = false;
        }
        return online;
    }

    public static UserSoap getUserFromSource(String userCode, ServiceContext serviceContext) {
        String userServiceURLString = "";
        String sourceSystemUrlStart = getSourceSystemUrlStartClean(serviceContext);
        String sourceSystemUsername = (String) serviceContext.getAttribute("sourceSystemUsername");
        String sourceSystemEncryptedPassword = (String) serviceContext.getAttribute("sourceSystemEncryptedPassword");
        String decryptKey = (String) serviceContext.getAttribute("decryptKey");
        int retryCounter = 0;
        long retryTimeout = 0;
        UserSoap user = null;
        retryCounter = getAttributeInt(serviceContext, "retries");
        retryTimeout = getAttributeLong(serviceContext, "retryTimeout");

        try {
            userServiceURLString = sourceSystemUrlStart + "/user-portlet/api/user-service";
            LOGGER.debug("Contacting URL: " + userServiceURLString);
            URL userServiceURL = new URL(userServiceURLString);

            UserServiceSoapServiceLocator locatorUser = new UserServiceSoapServiceLocator();
            UserServiceSoap userServiceSoap = locatorUser.getUserService(userServiceURL);
            ((UserServiceSoapBindingStub) userServiceSoap).setUsername(sourceSystemUsername);
            ((UserServiceSoapBindingStub) userServiceSoap).setPassword(CryptoUtils.cryptoString(sourceSystemEncryptedPassword, decryptKey, "decrypt"));

            UserSoap[] users = userServiceSoap.getUsersByCode(userCode);

            if (users.length == 0) {
                LOGGER.debug("No users returned, ignoring " + userCode);
            } else {
                user = users[0];
            }

        } catch (MalformedURLException mue) {
            LOGGER.error("MalformedURLException with: " + userServiceURLString);
            LOGGER.debug(mue.getStackTrace());
        } catch (ServiceException se) {
            LOGGER.error("Failed getting a service");
            if (retryCounter > 0) {
                LOGGER.debug("Re-attempting this operation, remaining re-tries are: " + retryCounter);
                pauseTimer(retryTimeout * 1000);
                serviceContext.setAttribute("retries", String.valueOf(retryCounter - 1));
                user = getUserFromSource(userCode, serviceContext);
            } else {
                LOGGER.error("Retried enough times, halting.", se);
            }
        } catch (RemoteException re) {
            LOGGER.error("Something failed remotely");
            if (retryCounter > 0) {
                LOGGER.debug("Re-attempting this operation, remaining re-tries are: " + retryCounter);
                pauseTimer(retryTimeout * 1000);
                serviceContext.setAttribute("retries", String.valueOf(retryCounter - 1));
                user = getUserFromSource(userCode, serviceContext);
            } else {
                LOGGER.error("Exhausted number of re-tries, giving up", re);
            }
        }
        return user;
    }
}
{% endhighlight %}



There were a couple of things that had their own domain here:

1. Checking if a server is reachable
2. Make requests to the source server
3. Fault-tolerance - retry request n times before giving up


### 1. Checking if a server is reachable

I wrapped this logic as being a `Ping`:

{% highlight java %}
public class Ping {
  private final URL url;
  private final int timeoutInMilliseconds;

  public Ping(final URL url, final int timeoutInSeconds) {
      this.url = url;
      this.timeoutInMilliseconds = timeoutInSeconds * 1000;
  }

  public final boolean didSucceed() {
      return Try.toGet(() -> successful(ping())).map(Boolean::booleanValue).orElse(false);
  }

  private final Boolean successful(HttpURLConnection connection) throws IOException {
      return connection.getResponseCode() == 200;
  }

  private final HttpURLConnection ping() throws IOException {
      HttpURLConnection connection = (HttpURLConnection) url.openConnection();
      connection.setConnectTimeout(timeoutInMilliseconds);
      connection.setRequestMethod("GET");
      connection.connect();
      return connection;
  }
}
{% endhighlight %}



### 2. Make requests to the source server

Here I am only including the method that was fetching users, but there were other methods to fetch other entities. These other fetching methods were pretty much a copy of each other, changing only in what was really necessary.
This is not surprising as big code becomes scary to analyze and to improve.

Furthermore, the soap services returned either null, an exception or, by last, the expected value - which I didn't want to work with directly.

In the end, there were two cases that matter: the happy path - where the value was fetched successfully - or the failure (no value) one, which was fine enough to happen as long as it as logged.

My approach was therefore to create a `Service` for each of these entities and then wrap them using the `Facade` pattern.

Like this, I could wrap the bad bits of these soap services and make it bearable within the project I had to manage.

The `Service` to fetch Users looks like this:

{% highlight java %}
final class SourceUserService {
    private final UserServiceSoap service;
    private final String serviceURI = "/user-portlet/api/user-service";

    public SourceUserService(final SourceSettings settings) throws MalformedURLException, ServiceException {
        URL serviceURL = new URL(settings.hostname() + serviceURI);
        UserServiceSoapServiceLocator locatorUser = new UserServiceSoapServiceLocator();
        service = locatorUser.getUserService(serviceURL);
        ((UserServiceSoapBindingStub) service).setUsername(settings.username());
        ((UserServiceSoapBindingStub) service)
                .setPassword(CryptoUtils.cryptoString(settings.encryptedPassword(), settings.decryptKey(), "decrypt"));
    }

    public final UserSoap getUserByCode(final String code) throws RemoteException {
        List<UserSoap> users = Arrays.asList(service.getUsersByCode(code));
        return users.isEmpty() ? null : users.get(0);
    }
}   
{% endhighlight %}

And the Facade looks like this:

{% highlight java %}
public final class SimpleSourceAssetsFacade implements SourceAssetsFacade {
  private final SourceUserService userService;
  private final SourceSettings settings;
  private final FaultTolerantAttempt attempt;
  private static final Log LOG = LogFactoryUtil.getLog(SimpleSourceAssetsFacade.class);

  public SimpleSourceAssetsFacade(final SourceSettings settings)
                                throws MalformedURLException, ServiceException {
      this.settings = settings;
      userService = new SourceUserService(settings);
      attempt = new FaultTolerantAttempt(settings.retryTimeout(), settings.retries());
  }

  public final boolean isAvailable() {
      return new Ping(settings.hostURL(), settings.connectionTimeout()).didSucceed();
  }

  public final Optional<UserSoap> userByCode(final String code) {
      return attempt.to(() -> userService.getUserByCode(code));
  }
}
{% endhighlight %}


### 3. Fault-tolerance

To add the capacity of being fault-tolerant, I first found some libraries doing it but only through annotations.
These annotations required a parameter, which had to be defined at compile time.
That wouldn't work, as I needed to keep the capacity of changing the number of retries in the settings of this portlet.

I went, therefore, ahead with creating my own way of tolerating faults.


{% highlight java %}
public class FaultTolerantAttempt {
    private final int retryTimeout;
    private final int timesToRetry;
    private static final Log LOG = LogFactoryUtil.getLog(DirectSourceAssetsFacade.class);


    public FaultTolerantAttempt(int retryTime, int timesToRetry ) {
        this.retryTimeout = retryTime * 1000;
        this.timesToRetry = timesToRetry;
    }

    public <T> Optional<T> to(FallibleAction<T> action) {
        for (int i = 0; i < timesToRetry; i++) {
            try {
                return Optional.ofNullable(action.result());
            } catch ( Exception e ) {
                LOG.info("Exception on FaultTolerantAttempt :: " + (i+1) + "/" + timesToRetry, e);
                pauseThread(retryTimeout);
            }
        }
        return Optional.empty();
    }

    private void pauseThread(long timeout) {
        LOG.info("Pausing Thread for "+(timeout/1000)+" seconds...");
        Try.to( () -> Thread.sleep(timeout),
                () -> LOG.info("Resuming the Thread..."),
                () -> LOG.error("Failed to pause the Thread!")
        );
    }
}
{% endhighlight %}

Being `FallibleAction` a simple functional interface:

{% highlight java %}
public interface FallibleAction<T> {
    T result() throws Exception;
}
{% endhighlight %}



### Conclusion

There were a couple of things I couldn't change.
For instance, UserServiceSoap (an external service) had no other way to be initialized but to have a locator and those setters in place after the casts. The best I could find was to wrap it as I showed above.

It gave me a great joy to take some of my free time do this refactoring and to increase considerably - I believe - the quality of the code, making it both object-oriented and functional, reusable, readable and maintainable.

Equally joyful was to get my friend who was working on this enthusiast about clean code and about functional programming.
