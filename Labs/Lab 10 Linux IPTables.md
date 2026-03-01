

IPTables is Linux firewall program and command line utility to configure kernel packet filtering rules originally developed by Netfilter.

Iptables monitors traffic from and to your server using tables. These tables contain sets of rules, called chains, that will filter incoming and outgoing data packets.

It’s the default firewall management utility on Linux Systems.

## Tables

Tables reside at the top of the hierarchy, their function and distinguishing factor being the ***roles*** the rules they contain provide.

A table also contains multiple **chains** which are essentially a list of rules, grouped by the **point** in the flow of the packet. (e.g. just as they arrive on the NIC, just as they leave etc.) More on this in the next section.

*The tables and their roles are as follows;*

#### The filter table

This is the default and perhaps the most widely used table. This table contains the rules that provides the actual firewall functionality, it lets you decide whether a packet should be allowed or denied to reach its destination.

#### The nat table

This table contains rules that allows you to route packets to different hosts on NAT networks by **changing the source and destination addresses of packets**. It is often used to allow access to services that can’t be accessed directly, because they’re on a NAT network.

#### The mangle table

This table contain the rules that allows you to alter packet headers and other forms of packet alteration.

#### The raw table

The *raw* table allows you to work with packets before the kernel starts tracking its state. (For example, a packet could be part of a new connection, or it could be part of an existing connection)  
In addition, you can also exempt certain packets from the state-tracking machinery. This basically means its used to let certain packets bypass the firewall.

#### The security table

The security table is consulted after the filter table to implement mandatory access control network rules. i.e. — it is used by SELinux to set SELinux context markers on packets.


## Installation

IPTables the default firewall management utility on Linux Systems comes pre-installed in most distributions. However, if you don’t have it in Ubuntu/Debian system by default, follow the steps below:

```c
sudo apt-get update
sudo apt-get install iptables
```

Check the status of your current iptables configuration by running

```c
sudo iptables -L -v
```

By Default, everything is allowed, for instance if we have Web Server Listening on port 80 & 443 (SSL port), then we can create the below rules

```c
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
## in case you have Email Server you can allow smtp port like this
sudo iptables -A INPUT -p tcp --dport 25 -j ACCEPT
```

Before that we have to add allow traffic from local interface

```c
sudo iptables -A INPUT -i lo -j ACCEPT
```

Output of the command will be

We can drop traffic coming from single IP address like this

```c
sudo iptables -A INPUT -s 10.0.5.6 -j DROP
```

If you want to drop rules for range of IP address

```c
sudo iptables -A INPUT -m iprange --src-range 10.0.8.1-10.0.8.255 -j DROP
```

Finally, you drop traffic for others.

```c
sudo iptables -A INPUT -j DROP
```

Flushing the whole tables

```c
sudo iptables -F
## Removing specific Line, you need to get list of Lines numbers
sudo iptables -L --line-numbers
## The select from the output the lines to delete
sudo itables -D <Line Number>
```

Saving the changes is as below:

```c
sudo iptables-save > /etc/sysconfig/iptables
```

if you want to restore the configuration

```c
sudo iptables-restore < /etc/sysconfig/iptables
```


### Command Syntax

Lets take a look at the command syntax specified in iptables itself;

```c
sudo iptables [option] CHAIN_rule [-j target]
```

An example command;

```c
sudo iptables –A INPUT –s 192.168.0.27 –j ACCEPT
```

The same command in long format;

```c
sudo iptables --append INPUT –-source 192.168.0.27 –-jump DROP
```

For the sake of easier comprehension we will only be using the long format in this article, you can switch to the short format in your real use.Now you know how the commands look like, lets understand them. The best way to do this is by explaining real scenarios that we might run into.

### Scenario 1: Blocking IPs

One of the most common use cases of a firewall, this is how we do it.

```c
iptables \
--table filter \             # Use the filter table
--append INPUT \             # Append to the INPUT chain
--source 59.45.175.62 \      # This source address
--jump REJECT                # Use the target Reject
```

If you want to delete this exact rule;

```c
iptables \
--table filter \             # Use the filter table
--delete INPUT \             # Delete from the INPUT chain
--source 59.45.175.62 \      # This source address
--jump REJECT                # Use the target Reject
```

or if you just want to change the IP;

```c
iptables \
--table filter \             # Use the filter table
--replace INPUT \            # Replace from the INPUT chain
--source 59.45.175.62 \      # This source address
--jump REJECT                # Use the target Reject
```

Note that you don't have to specify the filter table, since it is the default table, it will automatically be used if no table is specified.

### Scenario 2: Adding a rule to a specific position in a chain

To do this, we will first print out an ordered list of the current rules, this can be done by;

```c
iptables --list --line-numbers
```

which will give an output similar to;

```c
Chain INPUT (policy ACCEPT)
num target prot opt source destination
1 DROP all -- 59.45.175.0/24 anywhere
2 DROP all -- 221.194.47.0/24 anywhere
3 DROP all -- 91.197.232.104/29 anywhere
Chain FORWARD (policy ACCEPT)
num target prot opt source destination
Chain OUTPUT (policy ACCEPT)
num target prot opt source destination
1 DROP all -- anywhere 31.13.78.0/24
```

In this scenario assume you want to block the entire IP Block 59.45.175.0/24, all except one address from it, 59.45.175.10. We have used the same process as earlier already to block the IP block. Now we want to whitelist the exception address, Since iptables evaluates rules in the chains one-by-one, you simply need to add a rule to “accept” traffic from this IP above the rule blocking 59.45.175.0/24. So in this case you just need to run the command;

```c
iptables \
--table filter \
--insert INPUT 1 \
--source 59.45.175.10 \
--jump ACCEPT
```

If you list again now, you will see;

```c
Chain INPUT (policy ACCEPT)
num target prot opt source destination
1 ACCEPT all -- 59.45.175.10 0.0.0.0/0
2 DROP all -- 59.45.175.0/24 0.0.0.0/0
3 DROP all -- 221.192.0.0/20 0.0.0.0/0
4 DROP all -- 91.197.232.104/29 0.0.0.0/0
```

You have now inserted a rule into a chain.

### Scenario 3: Modifying Default Policy of a chain

We have discussed default policies in the introduction, it is simply what is done to a packet if none of the rules in a chain match, the default chains have an accept policy by default. Perhaps you’re configuring iptables for your home computer, and you don’t want any local programs to receive inbound connections. To change this use;

```c
iptables \
--policy INPUT DROP
```

### Scenario 4: Blocking access to ports

To block access to SSH port for a range;

```c
iptables \
--append INPUT \
--protocol tcp \             # Specify TCP protocol
--match tcp \                # Load the TCP module
--dport 22 \                 # Destination port 
--source 59.45.175.0/24 \
--jump DROP
```

To block access to multiple ports, i.e. SSH and VNC access for a range;

```c
iptables \
--append INPUT \
--protocol tcp \
--match multiport \         # Load multiport module
--dports 22,5901 \
--source 59.45.175.0/24 \
--jump DROP
```

To block access to all ports except the ones specified, i.e. all except HTTP, HTTPS and SSH (ideal for web server);

```c
iptables \
--append INPUT \
--protocol tcp \
--match multiport \
! --dports 22,80,443 \
--jump DROP
```

To block Ping/ICMP requests;

```c
iptables \
--append INPUT \
--jump REJECT \
--protocol icmp \
--icmp-type echo-request
```

Block with no error message;

```c
iptables \
--append INPUT \
--protocol icmp \
--jump DROP \
--icmp-type echo-requestANDiptables \
--append OUTPUT \
--protocol icmp \
--jump DROP \
--icmp-type echo-reply
```

### Scenario 4: Using Connection Tracking — Block incoming traffic but still access services

If you block IP on INPUT chain, you will find that you cannot access any services on that IP too, While that IP cannot access your host, you should still be able to access that IP. The reason why this is not working is because while your requests are reaching that server, iptables is dropping the responses coming to your hosts.

We can still achieve this, because iptables is a stateful firewall and has a connection tracking module for this purpose. The state tracked by this module can be either one of;

- **NEW:** This state represents the very first packet of a connection.
- **ESTABLISHED:** This state is used for packets that are part of an existing connection. For a connection to be in this state, it should have received a reply from the other host.
- **RELATED:** This state is used for connections that are related to another *ESTABLISHED* connection. An example of this is a FTP data connection — they’re “related” to the already “established” control connection.
- **INVALID:** This state means the packet doesn’t have a proper state. This may be due to several reasons, such as the system running out of memory or due to some types of ICMP traffic.
- **UNTRACKED:** Any packets exempted from connection tracking in the raw table with the NOTRACK target end up in this state.
- **DNAT:** This is a virtual state used to represent packets whose destination address was changed by rules in the *nat* table.
- **SNAT:** Like DNAT, this state represents packets whose source address was changed.

Therefore we can use the RELATED and ESTABLISHED states for connections that are initiated by us. To do this we can use the following command;

```c
iptables \
--append INPUT
--match conntrack 
--ctstate RELATED,ESTABLISHED \
--jump ACCEPT       # Accept packets in above connection states
```

### Scenario 5: Security Rules in iptables

To block Christmas tree packets aka kamikaze packets, which have all headers set so we need to simply drop packets with all headers. We can use command;

```c
iptables \
--append INPUT \
--protocol tcp \
--match tcp \
--tcp-flags ALL FIN,PSH,URG \
--jump DROP
```

To block common invalid packets such as packets with SYN and FIN set, we can simply look for packets with both set. To do this use;

```c
iptables \
--append INPUT \
--protocol tcp \
--match tcp \
--tcp-flags SYN,FIN SYN,FIN \
--jump DROP
```

And also packets that are a ‘new connection’ that doesn't begin with a SYN. To do this you simply need to check the FIN, RST, ACK and SYN flags; however only SYN should be set. Then, you can negate this condition. Finally, you can use conntrack to verify if the connection is new. Thus, the rule is:

```c
iptables \
--append INPUT \
--protocol tcp \
--match conntrack \
--ctstate NEW 
--match tcp ! \
--tcp-flags FIN,SYN,RST,ACK SYN \
--jump DROP
```

### Scenario 6: Rate Limiting using iptables

This is done by using the limit module, it has two main parameters; ‘limit-burst’ acts as a buffer and is the buffer size, if this buffer size is exceeded all packets are dropped. The rate you can refill this buffer is the ‘limit’.

This can be done with 2 commands, first you must change the default policy of the INPUT chain to drop and then use; (i.e. rate limiting ICMP packets)

```c
iptables \
--append INPUT \
--protocol icmp \
--match limit \
--limit 1/sec \
--limit-burst 1 \
--jump ACCEPT
```

You can also perform rate limiting **dynamically** for specific IP addresses, however this can be only performed by the recent module. It takes two commands and is done as follows;(i.e. Rate Limiting SSH per IP)

```c
iptables \
--append INPUT \
--protocol tcp \
--match tcp \
--dport 22 \
--match conntrack \
--ctstate NEW \
--match recent \
--set \
--name SSHLIMIT 
--rsourceiptables \
--append INPUT \
--protocol tcp \
--match tcp \
--dport 22 \
--match conntrack \
--ctstate NEW \
--match recent \
--set \
--name SSHLIMIT \
--update \
--seconds 180 \
--hitcount 5 \
--name SSH \
--rsource \
--jump DROP
```

### Scenario 7: Logging packets using iptables

This is fairly simple and done using;

```c
iptables \
--append INPUT \
--protocol tcp \
--match tcp \
--tcp-flags FIN,SYN FIN,SYN \
--jump LOG
--log-prefix=iptables:
```

The ‘ — log-prefix=’ is optional and is added so you can easily search the log later. The log is usually stored at `/var/log/syslog` or `/var/log/messages`.

### Scenario 8: Port redirection In same machine

To redirect a port in your machine to another port, i.e. HTTP port to 8080

```c
iptables \
--table nat \
--append PREROUTING \
--protocol tcp \
— dport 80 \
--jump REDIRECT \
--to 8080
```

### Scenario 9: Turning iptables into a proxy!

![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*8uD1C9dNOstRh0IM4lJ5bA.png)

Watch this amazing [video](https://www.youtube.com/watch?v=NAdJojxENEU) by the great Hussein Nasser, he will explain it better than I ever could. Below are the iptables rules;

```c
iptables \
--in-interface wlan0 \
--append PREROUTING \
--table nat \
--protocol tcp \
--destination 192.168.254.47 \
--dport 81 \
--jump DNAT \
--to-destination 192.168.254.10:8080iptables \
--append POSTROUTING \
--table nat \
--protocol tcp \
--destination 192.168.254.10 \
--dport 8080 \
--jump SNAT \
--to-source 192.168.254.47iptables \
--table nat \
--append POSTROUTING \
--protocol tcp \
--dport 81 \
--jump MASQUERADE
```

**Bonus Scenarios**:

- iptables as a load-balancer? — also by Hussein check it out [here](https://www.youtube.com/watch?v=-CraNvj48J0)!
- [*Custom*](https://www.booleanworld.com/depth-guide-iptables-linux-firewall/) chains in iptables
- The [owner](https://www.booleanworld.com/depth-guide-iptables-linux-firewall/) module for rules only for internal users

---
