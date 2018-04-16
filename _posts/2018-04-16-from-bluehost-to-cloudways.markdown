---
layout: post
title: From Bluehost to Cloudways
date:  2018-04-16 21:20:52 +02
categories: wordpress cloud hosting dns volunteering
comments: true
---


In my spare time, I volunteer as the webmaster of A Rocha Portugal, an environmental organization based in Portugal and with branches all over the world. My first assignment, in 2012, was to build their new websites, using Wordpress. After almost 5 years of being promised a decent hosting at Bluehost, I finally got the time to find good alternatives. Here I share how I made their websites 20 times faster by moving to [Cloudways](https://cloudways.com).

The websites referred to hereby are the [arocha.pt](https://arocha.pt) and the [arochalife.com](https://arochalife.com)

Bluehost is considered one of the best Wordpress hosting solutions on the market, although our experience never proved that to be true.
Our websites always ran pretty slow since day 0. It was not just the website that was failing the expectations. It was overall slow and buggy: logging in, managing the CPANEL, accessing the database, contacting their support, finding the root of whatever problem, etc.

"Why taking so long to move away from it then?", you may ask. The main reason was lack of time. Since I have completed their websites, when I was still at University, I only have spare time available to help out. Not only that but searching for alternative solutions only lead to learning once again that Bluehost was one of the best out there. Marketing in action...

We tried a few of their package deals. The shared hosting was a complete fiasco in terms of performance and security. Their special Wordpress hosting solution was our last hope, which showed a small improvement but nothing you could expect considering the costs invested in serving such simple websites. Their email service was also pretty bad. Our emails were considered as spam by some services, and getting a dedicated IP didn't make the trick. Their free and automatic renewable SSL was it all but automatic. Their support, although always polite, never really addressed any of the issues properly and contacting them meant allocating ridiculous amounts of time.

### Alternatives

Right before the annual renewal, last March, I finally got some spare time to seriously look for and consider a full migration to another service.
While looking around for good alternatives, I found Cloudways. I was initially keen to try DigitalOcean but given my lack of time, Cloudways became highly appealing, since it is a managed cloud service, meaning it deals a good deal of things for you.

Among the many nice things that Cloudways had to offer, one was definitely their migration plugin. I was afraid it would be just another Wordpress migration that didn't work... but I was wrong.

I was very impressed that **everything** worked well straight
away. I was curious about how the performance compared to live versions of the
websites and I was astonished by the difference. Note that this was before any
kind of optimization on my side whatsoever.

__arocha.pt__
<img src="/images/performance-arochapt.jpeg"/>

__arochalife.com__
<img src="/images/performance-arochalife.jpeg"/>

There are some factors to consider here, however:

- Some latency was to be expected from Bluehost since their servers are somewhere in the US.
- The live websites had arguably more load than the Cloudways ones These websites have very low traffic though, so I couldn't make that an excuse.

At this point I was wondering if the base package would be enough in
terms of resources when running the websites live, considering they were so slow on Bluehost running with 2 CPU
cores and 2 GB of RAM. After a few days of performance testing using [LoadImpact](https://loadimpact.com)
and the sweet command line, I was confident that Cloudways was the service we
were so long looking for.

### Migrating the infrastructure

Now that moving out was clearly the right thing to do, it was important to
figure out exactly the steps to take and the best order to take them.

#### 1. Choosing an email hosting service

Email downtime was absolutely not acceptable, therefore I started by looking for email
hosting services. Cloudways offers email hosting too, but I deliberately wanted to have email and the websites hosted in different services.
Read more about ("Why you shouldn't host your email & website on the same server")[https://block81.com/articles/why-you-shouldnt-host-your-email-website-on-the-same-server]

At this point, I learned that migrating emails from one email hosting
service to another isn't really a thing. You basically have to create the same mailboxes in the new service that you want to keep from the still live one.

After looking for some interesting solutions for our use case, Namecheap's email
service seemed appealing to me, both price and features wise, so I went for it.

After creating the mailboxes in Namecheap's email service, I could test how the outgoing email was working. The ingoing was still bound to the mailboxes at Bluehost, given the MX records were still set that way there.

#### 2. Set up Cloudflare

This is an important part to retain. As I mentioned, I definitely didn't want any
email downtime but neither did I want any websites downtime. Having my domains at Namecheap pointing to Bluehost still, the first subtask here was to set up new websites on Cloudflare and configure all DNS records to have it completely ready for the switch.

This meant I had to add two groups of DNS records:

1. In order to have the requests to the website being served by Cloudways, I added:
- an  `A` record pointing to our Cloudways server IP address
- a `CNAME www` record pointing to `wordpress-abc-xyz.cloudwaysapps.com`   

2. To have the email records fully configured, I followed [this article's
   recipe](https://www.namecheap.com/support/knowledgebase/article.aspx/1340/2176/namecheap-private-email-records-for-domains-with-thirdparty-dns)

In the end, it looked like this:

<img src="/images/dns-cloudflare.jpeg"/>


#### 3. Update the Nameservers

At this point, I configured each website's Domain on Cloudways, which made Cloudways ready for the next steps.

Now that all the groundwork was done and ready for the final switch, I could finally
update each domain's nameservers to the pair of nameservers Cloudflare
has attributed to each website, accordingly.

Once the configuration was updated, it took about one hour to have the DNS propagation completed. I used [whatsmydns](https://www.whatsmydns.net/) to check
the progress during this period.


#### 4. Setup SSL

When setting a domain through a CDN such as Cloudflare, there are two
communications to be secured:

- the communication between clients and
the CDN
- the communication between the CDN and the server hosting your
website

First, I activated SSL on Cloudflare by choosing the `Flexible` mode. This mode means
that Cloudflare provides a secure connection between clients and itself but
tolerates a non-secure connection (plain HTTP) connection between itself and
your hosting server.

Secondly, I configured SSL on Cloudways for each of the websites. This took
another 30 minutes to be completed. I waited for about one hour before switching the SSL
mode on Cloudflare to `Full`, which means that the two communications described
above are to happen through HTTPS.

#### 5. Further optimizations

Having completed the previous steps, I had both websites fully functional and
ready to go live. Since I had myself going and working with the websites was
finally fast enough to keep myself sane, I went ahead with some further
optimizations.

The first and most relevant one was to install and setup [WP Super Cache](https://wordpress.org/plugins/wp-super-cache/),
which compiles a static html version of your website. The plugin supports
specifying specific pages you want to keep serving dynamically. It also handles
sessions well, meaning that if you log in you will not get static html.

Having a static version of your website means that your server only processes
the last version of each page once, instead of as many times are requests asking
for them. Having less PHP(-FM) processes running means that one needs less
power, which translates to saving money, which is great. Moreover, static
websites are also safer since your server only has to hand over html files.

It now became safer, more reliable, and even faster! Nice!

Other optimizations took place, such as resources mini and unification, image
quality optimization and so forth. I also changed Cloudway's default PHP-fm configuration
to avoid too many processes to be spawn and lowered the processing timeout to avoid having processes taking all the resources. All tiny bits and pieces that put together improve a website quality
and reputation, SEO wise.

### Conclusion

Bluehost proved to be a total disaster for us for a long period of time.
Having finally moved to a better service felt like a blessing both to me (on the
management side), but also to the whole team of A Rocha Portugal.

Not only the infrastructure is now reliable and fast, the migration also meant cutting costs. This might not be true if you use a service like Bluehost where mailboxes are included and unlimited, though. Our number of mailboxes was sufficiently low, but if you have a huge number of them you might end up paying more for the email service than for hosting itself.

Most pages now take an average of 700ms to be served. Counting on browser caching that time is much lower, making browsing the websites pleasantly fast.

If you are going through or thinking of doing some similar migration, feel free to reach out for help.

#### P.S.

Feel invited to contribute to the environmental and scientific work of A Rocha Portugal by [becoming a friend](https://arocha.pt/en/get-involved/become-a-friend/friendship-account/type-of-friendship/).

Also, if you enjoy Birdwatching, you should definitely consider enjoying an amazing week in southern Portugal with [A Rocha Life](https://arochalife.com/birdwatching/)
