---
layout: post
title:  "Mini polyglot grep"
date:   2016-05-17 18:00:39 +0100
categories: programming languages
comments: true
---

I wanted to write a little grep program that prints the lines of a given file that contain a given term.<br>
So I choose to write it in ```Haskell```, ```Ruby```, ```Java```, ```Scala``` and ```Python```. 

The aim was to get an idea on these points:

- how different these languages deal with IO
- how conditional flows could be managed
- how clean the code would be
- LOC


So as baseline I defined the following points:

- usage is grep term file (the term and the file to grep would be taken as program arguments)
- in case of incorrect usage (not enough arguments), prints the usage. 
- these program arguments should be named and not used directly
- the actual grep work should be defined in a specific function
- ignored cases such as incorrect filepaths

<br>
So I started with Haskell. <br>
Haskell has a very interesting way of dealing with IO. IO is part of the "dirty" world, not of the pure one. <br>
So computation that happens to do IO work is binded and "isolated" from the pure code and this separation is explicit, through the IO Monad.<br>
To give a quick simple example of what this implies, a String value obtained from a IO operation has type of IO String and not just String.

## Haskell version:

Lines: 9

{% highlight haskell %}
import Data.List
import System.Environment

main :: IO ()
main = getArgs >>= dispatch

dispatch :: [String] -> IO ()
dispatch (term:file:_) = grep term file
dispatch _ = putStrLn "usage: grep term file"

grep :: String -> FilePath -> IO ()
grep term file = readFile file >>= mapM_ putStrLn . filter (isInfixOf term) . lines
{% endhighlight %}


Why ```mapM_ putStrLn . filter (isInfixOf term) . lines``` ? <br>
Another way of writing this would be: ```putStrLn . unlines . filter (isInfixOf term) . lines```.
```mapM_``` is a function that takes a Monad (putStrLn) and a foldable element (the filtered lines) to which the function gets mapped upon.<br>
Both seem to me like fine solutions so I just decided to leave it like this.


## Java version:

Lines: ±24

{% highlight java %}
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

public class Main {

    public static void main(String[] args) {

        if ( args.length < 2 ) {
            System.out.println("usage: grep term file");
        }
        else {
            String term = args[0];
            String file = args[1];
            try { 
                grep(term, file);
            }
            catch (IOException e) {
                System.out.println("Got an IOException");
            }
        }
    }

    public static void grep(String term, String filepath) throws IOException {
        Files.lines(Paths.get(filepath)).filter(line -> line.contains(term))
                        .forEachOrdered(line -> System.out.println(line));
    }
}
{% endhighlight %}


Java 8 introduced Files which makes this work nicer than the tradicional while getLine().
Apart form that, not much.



## Ruby version:

Lines: 10 

{% highlight ruby %}
def grep(term, file)
    File.foreach(file) { |line| puts line if line.include? term }
end

if ( ARGV.length < 2 )
    puts "usage: grep term file"
else
    term = ARGV[0]
    file = ARGV[1]
    grep(term, file)
end
{% endhighlight %} 

Ok, maybe the foreach could be line-breaked but this looks pretty clean and readable.


## Scala version:

Lines: 18

{% highlight scala %}
import scala.io.Source

object Grep {
   def main(args: Array[String]): Unit = {
      if (args.size < 2)
         println("usage: grep term file")
      else {
         val term = args(0)
         val file = args(1)
         grep(term,file) 
      }
   }
   
   def grep(term: String, filepath: String): Unit = {
      Source.fromFile(filepath).getLines().foreach { line =>
         if (line.contains(term))
            println(line)
      }
   }
}
{% endhighlight %} 



## Python version:

Lines: 12

{% highlight python %}
import sys

def grep(term, file):
    with open(file) as file:
        for line in file:
            if term in line: 
                print line      

if len(sys.argv) < 3 :
    print "usage: grep term file"
else: 
    term = sys.argv[1]
    file = sys.argv[2]
    grep(term, file)

{% endhighlight %} 

<br>
I think the Ruby version ends up with a very readable core and looks quite clean, but that's all.<br>
the Python version doesn't look very interesting apart from the simplified syntax (like Ruby) without all the curly brackets that Java and Scala end up with. 

The Haskell version ends up being, in my view, the cleanest solution. If I would not expecify the function types (which I don't prefer) I would endup with only 6 lines to satisfy the requirements.<br>
The fact that I don't need to define flows in a if-else way but, instead, with functions and pattern matching, the way it reads so nicely and the way the language deals with IO as something apart (which I find more interesting and challenging)... It's just something else!


Find the [source repository here](https://github.com/NunoAlexandre/grep)

