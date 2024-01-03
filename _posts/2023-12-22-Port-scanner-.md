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

<img src="/assets/jpg/Tools.jpg" alt="Tools">

During this project, some of the tools I used were the following:

-   Microsoft Azure (Virtual machines, Microsoft Sentinel, Microsoft Defender for Cloud, Log Analytics workspace).
-   Windows Powershell.
-   SecurityEvent (Windows event logs).
-   Syslog (Linux event logs).

<img src="/assets/jpg/Portscanner.jpg" alt="portscanner">

In conclusion, it is important to understand that using Python we can socket an address family (such as AF_INET) to specify what the code will communicate with. We now have a deeper understanding of how tools like Nmap manage to function.

## Creating the honeypot

The first step was to create a Windows virtual machine and disable any security controls to attract Cyber attacks to then measure the security events. I then converted the logs using a third party website (https://ipgeolocation.io/) to gain more accurate locations of the logs.

## Importing the event log to Microsoft Azure

Using Microsoft Logs, the following query converted the logs I gathered to raw data, in order to display it on the attack map:
{% highlight js linenos %}
FAILED_RDP_WITH_GEO_CL 
| extend username = extract(@"username:([^,]+)", 1, RawData),
         timestamp = extract(@"timestamp:([^,]+)", 1, RawData),
         latitude = extract(@"latitude:([^,]+)", 1, RawData),
         longitude = extract(@"longitude:([^,]+)", 1, RawData),
         sourcehost = extract(@"sourcehost:([^,]+)", 1, RawData),
         state = extract(@"state:([^,]+)", 1, RawData),
         label = extract(@"label:([^,]+)", 1, RawData),
         destination = extract(@"destinationhost:([^,]+)", 1, RawData),
         country = extract(@"country:([^,]+)", 1, RawData)
| where destination != "samplehost"
| where sourcehost != ""
| summarize event_count=count() by timestamp, label, country, state, sourcehost, username, destination, longitude, latitude
{% endhighlight %}

<img src="/assets/jpg/Eventlogs.jpg" alt="Eventlogs">
The logs were actively recorded using Microsoft PowerShell throughout the project, providing a comprehensive dataset for analysis.

## Displaying results on the attack map

By using another query in Azure Sentinel, the recorded logs were then converted into a map which displayed the attacks by location. This was the result after the first hour of creating the honeypot:

<img src="/assets/jpg/Attackmap1.jpg" alt="Attackmap1">
(Failed brute force attempts shown on map)

After 24 hours of deploying the honeypot, the SIEM gathered and displayed the following:

<img src="/assets/jpg/Attackmap1.jpg" alt="Attackmap1">

The following table shows the metrics measured in the insecure environment for 24 hours: Start Time 2023-11-09 17:10:11 Stop Time 2023-11-10 17:15:42

<table class="table">
  <thead>
    <tr>
      <th>Metric</th>
      <th>Count</th>
    </tr>
  </thead>
  <tfoot>
    <tr>
      <td>Security incidents</td>
      <td>352</td>
    </tr>
  </tfoot>
  <tbody>
    <tr>
      <td>Security events</td>
      <td>23725</td>
    </tr>
    <tr>
      <td>Syslog</td>
      <td>3147</td>
    </tr>
    <tr>
      <td>Security Alert</td>
      <td>11</td>
    </tr>
  </tbody>
</table>

Security Enhancement and Outcome: Following the reinforcement of security controls, the project yielded promising outcomes. Security alerts and incidents dropped to zero, accompanied by a significant reduction in security events.

## Conclusion

This cybersecurity project leveraged Microsoft Azure for the creation and deployment of the honeypot. The integration of log sources into a Log Analytics workspace streamlined data management. Microsoft Sentinel played a pivotal role in triggering alerts and generating incidents based on the logs. By assessing the insecure environment before implementing security controls, comprehensive metrics were obtained to facilitate result comparison. The overarching aim was to evaluate the effectiveness of the implemented security measures.

Based on Josh Makador’s lab https://github.com/joshmadakor1/Sentinel-Lab


[^fn-sample]: Handy! Now click the return link to go back.
