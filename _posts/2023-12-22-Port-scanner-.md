---
layout: post
title: Port scanner with Python
lead: xxxx.
---

The objective of this project is to understand how tools like Nmap or Masscan work by implementing Python code to imitate port scanning.
{: .message }

- toc
{: toc }

First, we must import the socket library into our code editor and ask the user what IP will be used:
{% highlight js linenos %}

{% endhighlight %}

Next we will use a for loop to scan the ports.  We must take into account how many ports there are so we can include them all (65.535 ports):
{% highlight js linenos %}

{% endhighlight %}

We then create a socket:
{% highlight js linenos %}

{% endhighlight %}

socket.AF_INET utilizes IPv4 and socket.SOCK_STREAM specifies that we will use TCP protocol. We will also apply a 5 seconds timeout:
{% highlight js linenos %}

{% endhighlight %}

And now we will do the most interesting part of this activity. We will use the socket to connect to a port and verify if it is open or not. We must create a new variable and use both ip and port:
{% highlight js linenos %}

{% endhighlight %}

If the result is 0 then the port is open. We find out what port is open with the following code:
{% highlight js linenos %}

{% endhighlight %}

This will display the open port and close the socket. We also want to know if the port is closed, we finish with an else statement:
{% highlight js linenos %}

{% endhighlight %}

When we run the code, we will be asked to provide an ip address to be scanned (we will use a test IP provided by Nmap) and then as expected the closed and open ports will be displayed:

<img src="/assets/jpg/Portscanner.jpg" alt="Portscanner">

In conclusion, it is important to understand that using Python we can socket an address family (such as AF_INET) to specify what the code will communicate with. We now have a deeper understanding of how tools like Nmap manage to function.

[^fn-sample]: Handy! Now click the return link to go back.
