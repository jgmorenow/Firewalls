<h1>Filtering Network Traffic with Firewalls</h1>


<h2>Description</h2>
<b>Description: A firewall monitors and filters incoming and outgoing network traffic. There’s a misconception that the firewall is always the last line of defense but in reality, a perimeter firewall should be the first obstacle bad actors encounter when they try to penetrate any network.

In this project, I will use two firewall solutions: iptables and pfSense. Iptables is a common firewall often used as a host firewall in Linux. pfSense can be implemented either as an open source software firewall or as a hardware firewall, however in this project I will be using the open source software.

</b>
<br />
<br />
<h2>iptables</h2>

<b>Linux's iptables utility offers great flexibility in filtering traffic entering, traversing, or leaving a network. iptable organizes its rules in policy chains, lists of rules that analyze and match packets based on their contents. Each rule determines what the firewall will do with a packet that matches its definition. It might allow, reject or drop the packet. If the packet is allowed, it will pass the firewall unhindered, when it drops the packet, the firewall will discard the packet and send no response back to the sender. When the packet is rejected, the firewall discards the packet and sends a rejection message to the sender.
</b>
<br />
<br />


<h4>Three main types of policy chain: Input chains, output chains, and forward chains</h4>
- <b>Input chains determine whet</b>


<b>Fetch the path from the system and create a variable $Path and assign the path. Make sure to separate the drive and the path.
</b>
<br />
<br />

