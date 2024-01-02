---
layout: post
title: Siem & honeypot
lead: Attract worldwide cyberattacks and analize them.
---

In this project, the goal was to extract data from cyber attacks to a virtual machine that was purposely vulnerable to anyone in the world and record the results into a Log Analytics workspace. These results were used to build an attack map with Microsoft Sentinel and measure how effective security controls are.
{: .message }

- toc
{: toc }

## Technologies used

During this project, some of the tools I used were the following:

-   Microsoft Azure (Virtual machines, Microsoft Sentinel, Microsoft Defender for Cloud, Log Analytics workspace).
-   Windows Powershell.
-   SecurityEvent (Windows event logs).
-   Syslog (Linux event logs).

<img src="/assets/jpg/Tools.jpg" alt="Tools">

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
