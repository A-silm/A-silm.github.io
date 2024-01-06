---
layout: post
title: Wireshark threat analysis and filtering
lead: xxxx.
---

A simple Python program to scan ports.
{: .message }

- toc
{: toc }

## Technologies used:

During this exercise, we only used Wireshark to demonstrate its capabilities.

## Part 1: Analysis

In order to examine the suspicious traffic in the packet capture below, first the following must be established:

-   What is the issue?
> Suspicious traffic was reported within the network.
-   What is our scope?
> Target: traffic from 10.129.43.4.
> 
> When: last 48 hours. Verify whether the suspicious activity is still ongoing.
-   What are the target(s)?
> 10.129.43.4 and unknown protocol (port 4444).

By using XfreeRDP as a remote desktop protocol (RDP) to connect to the IP 10.129.43.4, Wireshark was able to gather the following information:

<img src="/assets/jpg/Filter1.jpg" alt="filter1">

After the packet capture, and by checking the conversation plugin we see there are three conversations in this capture. All of these conversations include our suspicious host. We will now verify what the traffic is by inspecting the protocol hierarchy:

<img src="/assets/jpg/Filter2.jpg" alt="filter2">

The protocol hierarchy reveals that the traffic is predominantly segmented into both UDP (11.4%) and TCP (79.5%). Our next step is to filter out noise to examine both UDP and TCP traffic:

<img src="/assets/jpg/Filter3.jpg" alt="filter3">

Applying the !tcp filter to isolate UDP traffic, we observe only nine packets. These include four ARP packets, four Network Address Translation (NAT) packets, and one Simple Service Discovery Protocol (SSDP) packet. By examining the packet types and the information within we can conclude it is normal network traffic. Next we will use the filter !udp && !arp to examine TCP traffic:

<img src="/assets/jpg/Filter4.jpg" alt="filter4">

We can see that at packet 3 there is a three-way handshake and the same ports are used in the output. Further exploration of the TCP stream from packet 3 allows a deeper understanding of the conversation:

<img src="/assets/jpg/Filter5.jpg" alt="filter5">

Within the conversation in the TCP stream, we see traces of suspicious activity. The first indication is the use of commands such as whoami, ipconfig. This can indicate the intruder wants to verify the access they gained within the network.
By continuing to examine the TCP stream, we find the following information:

<img src="/assets/jpg/Filter6.jpg" alt="filter6">

We can see the intruder also used the dir command and was successful in registering a new user to the administrators group. This confirms the corporate infrastructure was breached. This concludes our exercise as the next step is to send the information and evidence to the IR team.
We can conclude that the intruder used Netcat shell to directly interact with a host within the network and gather more information. The intruder used RDP from the host which triggered alarms and the need to capture the traffic to examine further.

## Part 2: Filtering and RDP decryption

After examining the evidence from the first part of this exercise, an RDP-key was found hidden in a folder hive on the host. After some research, we conclude we can use the key to decrypt RDP traffic and inspect it.
When filtering RDP traffic in the capture yielded no results:

<img src="/assets/jpg/Filter7.jpg" alt="filter7">

By default, RDP uses TLS to encrypt the data. By filtering a commonly used port for RDP, we can verify its existence (tcp.port == 3389)

<img src="/assets/jpg/Filter8.jpg" alt="filter8">

This confirms that a session was established between the two hosts over TCP port 3389. To decrypt the RDP traffic, we will use the key we found earlier in Wireshark:

<img src="/assets/jpg/Filter9.jpg" alt="filter9">

Now that the key was registered within Wireshark, we successfully filter RDP traffic and can now follow TCP streams, export objects or anything else that may help us on our investigation. The key was pulled by using OpenSSL from the server.

<img src="/assets/jpg/Filter10.jpg" alt="filter10">

If we filter again to check port 3389 we find a record labeled Ignored Unknown Record, which will show us the username “bucky” in the ASCII:

<img src="/assets/jpg/Filter11.jpg" alt="filter11">

## Conclusion

This concludes the second part of the exercise, illustrating Wireshark's capability to decrypt data for any protocol when utilizing the appropriate key to establish connections. The process demonstrated emphasizes the tool's effectiveness in enhancing forensic analysis and investigation efforts. The acquired information will be crucial for reporting and further actions, emphasizing the importance of integrating Wireshark in cybersecurity practices.

Source: https://academy.hackthebox.com/module/details/81

[^fn-sample]: Handy! Now click the return link to go back.
