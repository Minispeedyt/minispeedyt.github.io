---
layout: default
---
[![spanish image link](./images/es_small.png)](./es.html)

[Click here to change to spanish!](./es.html)
# Index
WIP :)
# Simple port scanner
[Link to the github repo.](https://github.com/Minispeedyt/simplescanner/tree/main)

1/31/2025
I wanted to create a simple port scanner using python, for this I decided that I was going to use scapy. The first version/prototype works well but you have to modify the code if you want to change the ports or the IP to scan. Here's the first version of the scanner:
```
from scapy.all import *
res,unans = sr(IP(dst="172.18.0.2")/TCP(flags="S", dport=(1,100)), timeout=1 )
for s,r in res:
    if r[TCP].flags == 0x12:
        print("Port %d is open" % s[TCP].dport)
    elif r[TCP].flags == 0x14:
        print("Port %d is closed" % s[TCP].dport)
    else:
        print("Port %d is closed or filtered.")
```
Let's break it down step by step to see what I did and how it works:
## What is a port scanner?
A port scanner is a tool used to find openings in a system's defenses. Let's imagine that a server is actually a castle with many doors. Some gates are open, welcoming travelers (or intruders). Others are locked, denying entry. And some gates are sealed shut, guarded by sentinels that don't allow anyone to enter. 
### Why is it useful?
A port scanner helps:
*    Ethical hackers and cybersecurity professionals to find weak points before attackers do.
*    System administrators to ensure only necessary ports are open, reducing security risks.

## How does it work?
It sends special network packets to different ports on a target machine.
It listens for responses, classifying ports as:
1.    Open → The service responds, meaning the port is in use.
2.    Closed → The target rejects the request, indicating the port isn’t active.
3.    Filtered → No response at all—possibly blocked by a firewall.

### What is a packet?
A packet is the smallest unit of data that travels across a network. It is a tiny fragment of a message, carrying information from one computer to another.
Each packet consists of three parts:
1.    Header – It carries essential instructions:
    *    Source IP (Where it came from)
    *    Destination IP (Where it should go)
    *    Protocol Information (Is it TCP? UDP? ICMP?)
    *    Packet Number (How it fits into the full message)
2.    Payload – The actual data, whether it be a piece of an email, a chunk of a video, or part of a webpage.
3.    Trailer (optional) – Sometimes, a checksum or error-checking data is added to verify the packet wasn’t corrupted in transit.

### How is a connection established between computers?
Usually a connection is established using what is known as a three way handshake, it looks like this:
1.    SYN → The user's device knocks on the server’s door, sending a SYN (synchronize) packet to request a connection.
2.    SYN-ACK → The server answers the knock, replying with SYN-ACK (synchronize-acknowledge), signaling it is ready.
3.    ACK → The user's device confirms by sending an ACK (acknowledge) packet, completing the handshake, and allowing data to flow.
If a port is closed, the handshake would instead look like this:
1.    SYN → The client knocks on the server’s door, sending a SYN (synchronize) packet to request a connection.
2.    RST-ACK → Instead of welcoming the request, the server slams the door shut, responding with RST-ACK (reset-acknowledge), forcefully rejecting the connection. After this, no further communication occurs.

Using this information, we can know whether the ports of a server or a device are open or closed.

## Sending packets
First, we import scapy and we use `sr(IP(dst="172.18.0.2")/TCP(flags="S", dport=(1,100)), timeout=1 )` to send the packets, where `IP(dst="172.18.0.2")` tells scapy which IP to send the packets to, `TCP(flags="S"` specifies that we want to send SYN packets (those are the packets used in the first step of a TCP handshake), `dport=(1,100)` means that we want to scan all the ports between 1 and 100 and lastly `timeout=1` tells scapy how much time to wait for a response. We store the answered packets in 'res' (sent and received pairs) and the packets that received no response in 'unans'.
## Finding open/closed ports
We create a for loop where we look at each sent and received packet (`for s,r in res:`), then for every received packet we check whether the response flag of it is SYN-ACK (0x12) or RST-ACK (0x14). This works because 0x12 converted to binary and then mapped to the flag positions of the response would look like this: 
*    NS = 0
*    CWR = 0
*    ECE = 0
*    URG = 0
*    ACK = 1
*    PSH = 0
*    RST = 0
*    SYN = 1
*    FIN = 0

If you do the same with 0x14, you'll see that you'll get RST-ACK. As mentioned before, SYN-ACK would mean that the port is open and RST-ACK would mean that it's closed.
For every sent packet we're going to use `s[TCP].dport` in a print statement so that we can tell the user the number of each open and closed port.
That's it for today, tomorrow I'll make it so that it's easier to change the ports and IP to scan and I'll add other QoL changes.
