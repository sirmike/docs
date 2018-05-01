# Intro
I quite often observe that programmers, especially those who are not so interested in operating systems, really struggle with networking. They tend to use common libraries and treat them like some kind of a black box. They just work, it's fine, move on to the next task. I bet anyone of us has been there. It's not a shame. But when we do that and don't even think what's under the hood we realize very quickly that we're missing something. And the real fun begins when our nice app / service / you_name_it behaves odd outside our local machine.

# Common problems, common mistakes
Problems that could have been predicted and avoided at designing stage came out in production? Well, it happens, but please, do not enter to "I have no idea what I'm doing but I need to fix that" mode. Get to know some tools and fundamentals, then at least you will understand where your problems come from.

One very common mistake that programmers do is that they don't sacrifice additional time to do some research. I see that especially when working with juniors (that's not bad to be a junior!). But they don't ask questions like "Did somebody had this problem in the past?" or "Is it a tool that could help me with that?". No, that would be too obvious, wouldn't it? :) And they start to struggle. They fix a problem sooner or later but with enormous waste of time.

Let's suppose that we need to debug communication between our application and a remote service. Sounds trivial? Ofcourse it does, but when remote part of this "system" is very complex and running it on the local machine could be time consuming we can end up with unnecessary coding very quickly. When we do not know tools we start using a programming language, that's very first thing that comes to our mind. And we start writing "very simple" webservice to debug our requests. And it's just one step away from Hell :) We quickly end up with kind of "Frankenstein" code just to handle and debug basic HTTP requests. There is better and quicker way to do that.

# Netcat
Meet Swiss Army knife of networking - Netcat. As pretty much every tool in Unix world, Netcat does one thing and does it right. It reads and writes data across network connections.

You want to create one-request data server? Here it is:

`nc -l 3000` - done, we're listening for incoming connections on port 3000. Server will write anything that we send to it to the standard output and close automatically when the first request finishes.

Parameters may be a bit different for an operating system that you use (I use MacOS), but the main idea remains the same. Linux implementation which does the same thing needs separate `-p` param: `nc -l -p 3000`. If you're not sure, read `man nc`.

So let's use our "server" and send something there. We can use Netcat for that too as this tool is just a pump for data over a network.

`echo "Foo" | nc localhost 3000`

WHOA! The server writes the data we just sent in to the standard output!

`Foo`

As I mentioned, the server outputs what we sent in. Hey, wait a minute! Can I... YES OFCOURSE YOU CAN!
The very first question that should come to your mind now is "Can I redirect this to a file?" :) Let's do that:

`nc -l 3000 > output.txt`

Then do the request once again:

`echo "Foo" | nc localhost 3000`

Voila, we have a file `output.txt` and `Foo` string inside. Does it mean that we can transfer other (larger? binary?) data like that? YEP, you can transfer anything you want that way :) That's the simplest way of sending files over the network. Seriously, you don't need Slack, Samba or FTP for that. Next time when your team mate needs a big file from you, and you're in the same network, just create one-time-netcat server to do that :) I assure you, sending big files from one desktop machine to another when you sit next to each other is sometimes more difficult than one might think. Been there, done that :D There is no security layer on top of that ofcourse, but hey, we just need to transfer some data quickly and we're programmers. We don't need fancy window tools for... sending files.

# HTTP

When we have our "server" we can easily debug HTTP requests. We will tell our "server" to return something for the first request that hits it:

`echo "Simple response" | nc -l 3000`

It's not a full HTTP response but simple enough to make it work. We're more interested in a request itself, than a response now. And we're programmers so we use tools carefully crafted for http requests. Let's do that with cURL:

`curl http://localhost:3000`

and the server prints http request body, that's what we wanted to know:

```
GET / HTTP/1.1
Host: localhost:3000
User-Agent: curl/7.54.0
Accept: */*
```

Cool, now we know how cURL does that. Don't worry if it looks cryptic, we'll cover the details in the next article.

# Credits

* http://nc110.sourceforge.net - Netcat homepage
* https://en.wikipedia.org/wiki/Netcat - What Netcat can do in a nutshell
* https://jvns.ca/blog/2013/10/01/day-2-netcat-fun/ - Fantastic place for getting knowledge about programming and operating systems, of one of my favourite bloggers, Julia Evans