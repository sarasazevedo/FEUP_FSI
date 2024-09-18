# Logbook 13

## Lab Environment (Task 2.1)

Initially, we executed the commands and add the new entries provided in the lab. 

```note
dcbuild               
dcup                  
``` 

![Alt text](/images/Logbook13-N1.png)

## Task 2.2

![Alt text](/images/Logbook13-N2.png)

![Alt text](/images/Logbook13-N3.png)

## Task 3.1

We used Scapy to do packet sniffing in Python programs. A sample code is provided in the following:

![Alt text](/images/Logbook13-N7.png)

![Alt text](/images/Logbook13-N6.png)

## Task 3.2

To spoof IP packets with an arbitrary source IP address we will spoof ICMP echo request packets, and send them to another VM on the same network. We will use Wireshark to observe whether our request will be accepted by the receiver. If it is accepted, an echo reply packet will be sent to the spoofed IP address.

![Alt text](/images/Logbook13-N4.png)

![Alt text](/images/Logbook13-N5.png)

## Task 3.3 

We need to use Scapy to estimate the distance, in terms of number of routers, between our VM and a selected destination. This is basically what is implemented by the traceroute tool. In this task, we will write our own tool. The idea is quite straightforward: just send a packet(any type) to the destination, with is Time-To-Live (TTL) field set to 1 first. This packet will be dropped by the first router, which will send us an ICMP error message, telling us that the time-to-live has exceeded. That's how we get the IP address of the first router. We then increase our TTL field to 2, send out another packet, and get the IP address of the second router. We will repeat this procedure until our packet finally reaches its destination. It should be noted that this experiment only gets an estimated result, because in theory, not all of these packets take the same route(but in practice, they may, within a short period of time).

![Alt text](/images/Logbook13-N8.png)

## Task 3.4 

![Alt text](/images/Logbook13-N9.png)

VM2 sends a ping to `1.2.3.4` which is a non-existing host on the Internet. Without the program we will get a 100% packet loss because it will never return to the source. We can see in the Wireshark logs that the ARP protocol is asking who has the IP `1.2.3.4` on the network. So the attacker(my program on VM1) returns with an answer to it and the ICMP packet reply is coming back to VM2.

VM2 sends a ping to `10.9.0.99` which is a non-existing host on the LAN. In this scenario we're getting the same concept with the same idea of the ARP protocol. Even though the host doesn't even exist. The program from VM1 will send back an ICMP response packet. 

VM2 sends a ping to `8.8.8.8` which is an existing host on the Internet. This scenario is different from the others because it really exists on the net. So in this case we're getting duplicate responses, that's because the real destination is responding to the source, but our program is also responding to the source.

![Alt text](/images/Logbook13-N10.png)