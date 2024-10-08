<h1>Filtering Network Traffic with Firewalls</h1>


<h2>Description</h2>
<b>A firewall monitors and filters incoming and outgoing network traffic. There’s a misconception that the firewall is always the last line of defense but in reality, a perimeter firewall should be the first obstacle bad actors encounter when they try to penetrate any network.

In this project, I will use one firewall solutions: iptables. Iptables is a common firewall often used as a host firewall in Linux. 
</b>


<h2>iptables</h2>

<b>Linux's iptables utility offers great flexibility in filtering traffic entering, traversing, or leaving a network. iptable organizes its rules in policy chains, lists of rules that analyze and match packets based on their contents. Each rule determines what the firewall will do with a packet that matches its definition. It might allow, reject or drop the packet. If the packet is allowed, it will pass the firewall unhindered, when it drops the packet, the firewall will discard the packet and send no response back to the sender. When the packet is rejected, the firewall discards the packet and sends a rejection message to the sender.
</b>
<br />
<br />


<h4>Three main types of policy chain: Input chains, output chains, and forward chains</h4>

- <b>Input chains - Determine whether to allow certain traffic into the network from an external source.</b>

- <b>Output chains - Indicate whether the firewall should allow certain outbound traffic to an external network.</b>

- <b>Forward chains - Forward the traffic your firewall receives to another network.</b>


<h2>Installing iptables</h2>

<b>I've already built a standard Ubuntu server, so now I can start configuring its iptables firewall.

Recent Ubuntu systems have iptables installed by default but we can always check by running the following command:
</b>
<p align="center">
<img src="https://i.imgur.com/TXg1Ou2.png" height="25%" width="75%" />
</p>

<b>If iptables is installed, the server should return the version information. If iptables is not installed, you'll receive an error, indicating you'll need to make the install.</b>


<b>Next, I'll install iptables-persistent, a tool that allows you to save your firewall configurations and automatically reload them after a reboot of the server.</b>

<p align="center">
<img src="https://i.imgur.com/oXtvCAQ.png" height="25%" width="75%" />
</p>

<b>An installation wizard will take over the terminal window and I'll be shown the file in which the server will save the firewall rules and informed that rules from this file will load ay system startup.

I'll also need to save any changes to firewall rules manually beyond this installation process, so I will select YES to save any current firewall rules. 
<p align="center">
<img src="https://i.imgur.com/GXioi9I.png" height="25%" width="75%" />
</p>
If I don't install this component, I will have to reconfigure my firewall every time I restart my server.
</b>

---
<b>Now let's go ahead and view our firewall rules. The command sudo iptables -L is used to list all the rules in the current IP tables on a Linux system. 

In the output, policy ACCEPT indicates that, by default, iptables accepts all traffic for input, output, and forwarding. This default behavior is desirable since it works without any user configuration. However, this solution is insecure. 
</b>

---
<h2>iptables Firewall Rules</h2>

<b>We need to keep in mind that order matters, when it comes to creating iptables rules. As traffic reaches your firewall, iptables checks its rules one after the other in the order they appear. 
</b>

<b>To understand how to contrust an iptables firewall rule, let's take a look at the following example:</b>

<p align="center">
<img src="https://i.imgur.com/WxoT4dt.png" height="25%" width="75%" />
</p>

<b>Immediately after sudo, iptables will begin the rule definition. The next argument determines whether the rule will be appended to (-A), deleted from (-D), or inserted into (-I) the specified policy chain. You can also specify (-R) to replace or update an existing rule. The INPUT indicates that a rule in the input chain is being modified. You can also specify OUTPUT, FORWARD, or other policy chains. In most cases, iptables needs to know the protocol and port to which the rules relate. In the example above, -p tcp indicates the rule will apply only to TCP traffic, and --dport 22 tells iptables that the rule applies to packets with a destination port of 22.</b> 

<b>The iptables firewall offer multiple matching modules, and you can specify the module to use with the -m argument. In the example provided, conntrack, a tool that allows stateful packet inspection, is used. Some other tools include connbytes, which creates rules based on the amount of traffic transferred, and connrate, which matches *on the transfer rate of the traffic.</b>
<b>Next, --ctstate tells iptables to allow and track traffic for the types of connections that follow NEW,ESTABLISHED. Finally, iptables will interpret -j and whatever follows it as the action to perform when this rule is matched. This will generally be, ACCEPT to all traffic matching this rule; DROP, or REJECT, to deny or block the traffic; or LOG to log the traffic to a logfile.</b>

---

<h2>Configuring iptables</h2>

<b>When configuring iptables, first add rules to drop invalid traffic. </b>
<p align="center">
<img src="https://i.imgur.com/IR1nMGE.png" height="25%" width="75%" />
</p>
<p align="center">
<img src="https://i.imgur.com/IvpUFD4.png" height="25%" width="75%" />
</p>

<b>Then, add rules to accept traffic related to existing connections, as well as established connections and the loopback address to aviod any issues. 
</b>

<p align="center">
  <img src="https://i.imgur.com/ESNl8IM.png" height="25%" width="75%" />
</p>
<p align="center">
  <img src="https://i.imgur.com/K2mCWV4.png" height="25%" width="75%" />
</p>
<p align="center">
  <img src="https://i.imgur.com/mqKZTZ3.png" height="25%" width="75%" />
</p>


<b>This allows the firewall to accept traffic matching a known connection or related to a connection in progress and discard any unexpected packets, keeping your network free from unsolicited or malicious network scanning activities.</b>
<b>Once these commands are run and rules are implemented to our policy chain, we can run the command to ruturn the list to ensure everything has been accepted:</b>

<p align="center">
<img src="https://i.imgur.com/A6gLYsN.png" height="25%" width="75%" />
</p>

<b>Now let's ensure that the firewall allows SSH traffic. We can do thi sin two ways: by broadly allowing SSH or by allowing SSH only from a subset of devices in my network. In this case I will allow SSH traffic originating from all devices in my network using the following command below:</b>

<p align="center">
<img src="https://i.imgur.com/sHLjr0x.png" height="25%" width="75%" />
</p>

<b>You should allow services like SSH only to and from specific IP addresses or ranges. Allowing remote access or file transfer between your endpoints and any other device is risky.</b>

<b>You can reduce your attack surface by specifying a source IP address or range in your input chain with the -s source option. I'm configuring iptables on a virtual machine, so I will choose to allow connections from a single host for management purposes and deny access to all other endpoints. </b>

<p align="center">
<img src="https://i.imgur.com/sHLjr0x.png" height="25%" width="75%" />
</p>

<b>If we run into a mistake, we can delete all the rules we've specified for any of our policy chains by using the -F chain or --flush chain, parameter:</b>


<b>Using the -P argument sets the default behavior of your policy chains and lets iptables know what to do with traffic that doesn't match your rules. I will set the behavior of my policy chain to DROP traffic. You will need to repeat the following for FORWARD AND OUTPUT</b>

<p align="center">
<img src="https://i.imgur.com/7h8QGfJ.png" height="25%" width="75%" />
</p>

<b>Now we will go ahead and check out iptables chain. We should notice the policy for all three chains has changed from ACCEPT to DROP, indicating the default behavior for each chain will drop traffic that doesn't match any of the rules created.</b>

<p align="center">
<img src="https://i.imgur.com/HeaqD2g.png" height="25%" width="75%" />
</p>

<b>Receving an error message indicating DNS failing is noraml, because the firewall is now blocking everything not explicitly allowed. This issue is resolved by the following rules.</b>

<p align="center">
<img src="https://i.imgur.com/IZTvx2i.png" height="25%" width="75%" />
</p>

<b>These commands append rules to the output chain, allowing the server to make outbound requests for domain name resolution on UDP and TCP port 53.</b>

<b>ICMP can be a useful troubleshooting tool that we may use so we will need to allow ping through the iptables firewall.</b>

<p align="center">
<img src="https://i.imgur.com/PgHioFF.png" height="25%" width="75%" />
</p>


<h2>Logging iptables Behavior</h2>

<b>Now that the iptables firewall is install and configured, we should tell it to log and produce records of traffic. Without logging, it will make troubleshooting issues difficult. </b>

<b>Let's create a new, custom policy chain. I'll name it LOGGING. The -N parameter is used to create new chains. Next, add a rule at the end of each of the INPUT and OUTPUT chains that tells iptables to send any traffic that hasn't yet matched a rule to the new LOGGING chain:</b>

<p align="center">
<img src="https://i.imgur.com/qgBtdW9.png" height="25%" width="75%" />
</p>

<b>Then, tell iptables to log only once per minute for each type of dropped packet:</b>

<p align="center">
<img src="https://i.imgur.com/EoAGFxE.png" height="25%" width="75%" />
</p>

<b>This limit is optional, and you can set it to any period, such as 1/second, 1/minute, 1/hour, or 1/day. Limiting the number of log entries reduces both the noise within and the size of the logfiles.


<b>Finally, the last command indicates to the firewall that, once logged, it can drop the packets.</b>

<p align="center">
<img src="https://i.imgur.com/6KQMsIR.png" height="25%" width="75%" />
</p>

<b>iptables firewall is now configured for logging. Now that we are finished, we need to remember to save our configurations. We will do this by running the following command:</b> 

<p align="center">
<img src="https://i.imgur.com/5sayZST.png" height="25%" width="75%" />
</p>








