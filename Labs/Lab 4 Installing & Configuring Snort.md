
## Introduction

SNORT is an **open-source, rule-based** Network Intrusion Detection and Prevention System **(NIDS/NIPS)**. It was developed and still maintained by Martin Roesch, open-source contributors, and the Cisco Talos team.

In this blog we are going to see working of snort as IDS and IPS. Lets dive in the topic.

##  Snort comes with fallowing capabilities:

- Live traffic analysis
- Attack and probe detection
- Packet logging
- Protocol analysis
- Real-time alerting
- Modules & plugins
- Pre-processors
- Cross-platform support! (Linux & Windows)

## Snort can be used in 3 modes:

- **Sniffer Mode —** Read IP packets and prompt them in the console application.
- **Packet Logger Mode —** Log all IP packets (inbound and outbound) that visit the network.
- **NIDS (Network Intrusion Detection System) and NIPS (Network Intrusion Prevention System) Modes —** Log/drop the packets that are deemed as malicious according to the user-defined rules.

## Understanding of IDS/IPS

Before diving into Snort and analysing traffic, I assume that you are familiar with what an Intrusion Detection System (IDS) and Intrusion Prevention System (IPS) is. if not then check my last blog.

- let’s verify snort is installed & Check version information.
```c
snort -V
```
- Validate your configuration file:

Here **“-T”** is used for testing configuration, and **“-c”** is identifying the configuration file **(snort.conf)**.

```c
sudo snort -c /etc/snort/snort.conf -T
```

\>> Configuration file is an all-in-one management file of the snort.

- Rules, plugins, detection mechanisms, default actions and output settings are identified here.
- It is possible to have multiple configuration files for different purposes and cases but can only use one at runtime.

“-q” prevents automatically shown default banner and initial information about your setup on every start.

### Operation Mode 1: Sniffer Mode

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*7scKYll8STIDW65d.png)

### Operation Mode 2: Packet Logger Mode

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*xq1S3v34QHXqOshj.png)

### Logfile Ownership

The fundamental file ownership rule; **whoever creates a file becomes the owner of the corresponding file**.

Snort needs superuser (root) rights to sniff the traffic, so once you run the snort with the “sudo” command, the “root” account will own the generated log files.

Therefore you will need “root” rights to investigate the log files. There are two different approaches to investigate the generated log files:

- Elevation of privileges: Use Sudo Command
- Changing the ownership of files/directories: `sudo chown username file` or `sudo chown username -R directory` The "-R" parameter helps recursively process the files and directories.

### Logging with parameter “-l”

```c
sudo snort -dev -l .
```

### Logging with parameter “-K ASCII”

```c
sudo snort -dev -K ASCII
```

The logs created with “-K ASCII” parameter is entirely different. There are two folders with IP address names. Let’s look into them.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*OHL2040yX-EFToBw.png)

Once we look closer at the created folders, we can see that the logs are in ASCII and categorised format, so it is possible to read them without using a Snort instance.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*3C_mvyPmxxbBoV4l.png)

In a nutshell, ASCII mode provides multiple files in human-readable format, so it is possible to read the logs easily by using a text editor. By contrast with ASCII format, binary format is not human-readable and requires analysis using Snort or an application like tcpdump.

### Reading generated logs with parameter “-r”

```c
sudo snort -r <log-filename>
```

**Note that** Snort can read and handle the binary like output (tcpdump and Wireshark also can handle this log format).

However, if you create logs with “-K ASCII” parameter, Snort will not read them.

**“-r”** parameter also allows users to filter the binary log files. You can filter the processed log to see specific packets with the **“-r”** parameter and Berkeley Packet Filters **(BPF).**

- `sudo snort -r logname.log -X`
- `sudo snort -r logname.log icmp`
- `sudo snort -r logname.log tcp`
- `sudo snort -r logname.log 'udp and port 53'`

Additionally, you can specify the number of processes with the parameter “-n”. **The following command will process only the first 10 packets:**

```c
snort -dvr logname.log -n 10
```

**Opening log file with tcpdump.**

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*rH7wUzSmdLmOQQWp.png)

### Operation Mode 3: IDS/IPS

IDS/IPS mode helps you manage the traffic according to user-defined rules.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*g-B6okPRTC9OIX2-.png)

### IDS/IPS mode with parameter “-c and -T”

```c
sudo snort -c /etc/snort/snort.conf -T
```

### IDS/IPS mode with parameter “-N”

```c
sudo snort -c /etc/snort/snort.conf -N
```

This command will disable logging mode. The rest of the other functions will still be available (if activated).

### IDS/IPS mode with parameter “-D”

```c
sudo snort -c /etc/snort/snort.conf -D
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*rjva3KP_UqoD7Jhe.png)

### IDS/IPS mode with parameter “-A”

**Remember that there are several alert modes available in snort:**

- **console:** Provides fast style alerts on the console screen.
- **cmg:** Provides basic header details with payload in hex and text format.
- **full:** Full alert mode, providing all possible information about the alert.
- **fast:** Fast mode, shows the alert message, timestamp, source and destination ıp along with port numbers.
- **none:** Disabling alerting.

### IDS/IPS mode with parameter “-A console”

Console mode provides fast style alerts on the console screen.

```c
sudo snort -c /etc/snort/snort.conf -A console
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*4u1ja-xUArmC08Xx.png)

### IDS/IPS mode with parameter “-A cmg”

Cmg mode provides basic header details with payload in hex and text format.

```c
sudo snort -c /etc/snort/snort.conf -A cmg
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*jUPokM8XB-bve9dD.png)

### IDS/IPS mode with parameter “-A fast”

Fast mode provides alert messages, timestamps, and source and destination IP addresses. **Remember, there is no console output in this mode.**

```c
sudo snort -c /etc/snort/snort.conf -A fast
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*KIVlvpsJz4REet0S.png)

### IDS/IPS mode with parameter “-A full”

Full alert mode provides all possible information about the alert. **Remember, there is no console output in this mode.**

```c
sudo snort -c /etc/snort/snort.conf -A full
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*KuCU46sYe-taIQPi.png)

### IDS/IPS mode with parameter “-A none”

Disable alerting. This mode doesn’t create the alert file. However, it still logs the traffic and creates a log file in binary dump format. **Remember, there is no console output in this mode.**

```c
sudo snort -c /etc/snort/snort.conf -A none
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*oYOin4iQ2Lq8YZjM.png)

### IDS/IPS mode: “Using rule file without configuration file”

It is possible to run the Snort only with rules without a configuration file. Running the Snort in this mode will help you test the user-created rules. However, this mode will provide less performance.

```c
sudo snort -c /etc/snort/rules/local.rules -A console
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*mJBFtSOKQCKop4m5.png)

### IPS mode and dropping packets

Snort IPS mode activated with **\-Q — daq afpacket** parameters. You can also activate this mode by editing snort.conf file.

Activate the Data Acquisition (DAQ) modules and use the afpacket module to use snort as an IPS: `-i eth0:eth1`

```c
sudo snort -c /etc/snort/snort.conf -q -Q --daq afpacket -i eth0:eth1 -A console
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*pK6TSGq-_zHtBj80.png)

## How to Install Snort

- To get started with Snort, boot up your Ubuntu machine and run the following command in the terminal:
  
```c
sudo apt install snort
```

- Once Snort is downloaded and installed, its configuration files are stored in the \`/etc/snort\` directory. This is where you’ll find important files like \`snort.conf\` and where you can define your rules, manage logs, and configure Snort for your specific needs.
  
```c
cd /etc/snort
```

## Using an ICMP Rule in Snort.

## Purpose:

This rule alerts whenever ICMP traffic is sent to the IP address `8.8.8.8`. It's useful for detecting ping requests or any other ICMP messages directed at that specific server.

Now, let’s set up a local rule for Snort. To begin, we need to edit the `local.rules` file where custom rules are defined. Use the following command to open the file:

```c
sudo nano rules/local.rules
```


![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*DxgYwDprvJfCganVfqz2yg.png)

We can simply type in our rule here but with some proper syntax.

```c
alert icmp any any -> 8.8.8.8 any (msg: "ICMP traffic to 8.8.8.8 detected"; sid:1000001; rev:1;)
```

Once you enter the above rule, hit **CTRL X, followed by Y, and press Enter** to save your rule.  
Breakdown of the Command:

- `**Alert**`: Triggers the rule as an alert
- `**icmp**`: The protocol being monitored is ICMP (used for network diagnostics, such as ping).
- `**any any**`: Refers to any source IP address and any source port.
- `**->**`: Indicates traffic direction (from source to destination).
- `**8.8.8.8 any**`: Destination is the IP address `8.8.8.8` and any destination port (though ICMP doesn't use ports).
- `**(msg: "ICMP traffic to 8.8.8.8 detected";**`: The message that will appear in the alert log if the rule is triggered.
- `**sid:1000001;**`: A unique Snort ID (SID) assigned to this rule. Custom rules typically start from 1,000,000 to avoid conflicts with built-in rules.
- `**rev:1;**`: The revision number of this rule. This can be incremented when the rule is modified.

### Applying the Snort rule

```c
sudo snort -A console -l /var/log/snort -i enp0s3 -c /etc/snort/snort.conf  -q
```

### Breakdown of the Command

- `Sudo`: Runs Snort with elevated permissions (required for network monitoring).
- `snort`: Executes the Snort IDS/IPS tool.
- `-A console`: Outputs alerts to the console.
- `-l /var/log/snort`: Logs packet data to the specified directory (`/var/log/snort`).
- `-i enp0s3`: Specifies the network interface (`enp0s3`) to monitor.
- `-c /etc/snort/snort.conf`: Uses the specified configuration file (`snort.conf`) to define Snort's rules and behavior.
- `-q`: Runs Snort in quiet mode (suppressing non-essential output).

6\. **Hit CTRL + Shift + T** on your terminal to open a new terminal. We will send a ping to Google 8.8.8.8 to see if our rule works.

As soon as we ping 8.8.8.8 from our new terminal, we see the below output on our 1st terminal, hence our rule is working fine.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*lCwZ-gVLlR_u1AXIwSxcGw.png)

## Rule: TCP connection to port 4444

## Purpose:

This rule will alert whenever a TCP connection is made to **port 4444** on any destination IP. Port 4444 is often used by **malware or remote shell exploits**, such as Metasploit’s reverse shells, making this a useful rule for detecting potential backdoor or unauthorized access attempts.Similarly, navigate to the `local.rules` file using the previous command. Once inside the file, you can enter your new rule.

```c
alert tcp any any -> any 4444 (msg: Connection to remote IP on port 4444; sid:1000002; rev:1;)
```

Hit **CTRL X, followed by Y, and press Enter** to save your rule.

### Breakdown of above Rule

- `**Alert**`: Triggeres the rule as an alert
- `**tcp**`: The protocol to monitor (TCP traffic).
- `**any any**`: Monitors traffic from any source IP address and any source port.
- `**->**`: Indicates traffic direction (from source to destination).
- `**any 4444**`: Matches traffic destined for **any IP address** on **port 4444** (a port commonly associated with remote shell services like Metasploit).
- `**(msg: "Connection to remote IP on port 4444";**`: The message to display when the alert is triggered.
- `**sid:1000002;**`: A unique **Snort ID (SID)** for this rule.
- `**rev:1;**`: The revision number of the rule, which can be updated if the rule is modified.

**Apply the snort rule**

```c
sudo snort -A console -l /var/log/snort -i enp0s3 -c /etc/snort/snort.conf  -q
```
- Now, we’ll craft a special packet using **hping** to trigger our Snort rule. Open a new terminal and use the following command to send a packet to port 4444:
```c
sudo hping3 -c 1 -p 4444 -S example.com
```

## Breakdown of the Command:

- `**sudo**`: Runs the command with elevated privileges (required for raw packet manipulation).
- `**hping3**`: A network tool used for sending custom TCP/IP packets and testing various network protocols.
- `**-c 1**`: Sends exactly **1 packet**.
- `**-p 4444**`: Targets **port 4444** on the destination (`example.com`).
- `**-S**`: Sends a **TCP SYN** packet, which is typically used to initiate a TCP connection (part of the 3-way TCP handshake).
- `**example.com**`: The target domain name or IP address to which the packet is being sent.

## Purpose:

This command sends a single **SYN** packet to port **4444** on `example.com`. It could be used to test if that port is open on the target host (similar to a **stealthy scan**), or as part of network security testing, such as simulating the initiation of a connection to that port.

Once you run the **hping** command, the Snort rule should successfully trigger, and you’ll see an alert similar to this in the Snort output:

![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*9VP1Y5gw4m_9qbPMM7_vNw.png)

## Snort Rules Examples

## 1\. A Rule to Detect a Simple HTTP GET Request to a Certain Domain

This rule will create an alert if it sees a TCP connection on port 80 (HTTP) with a GET request to the domain “example.com.”

```c
alert tcp anyany -> any 80 (msg: "Possible HTTP GET request"; content: "GET"; http_method; content: "example.com"; http_host; sid:1000001; rev:1;)
```

## 2\. A Rule to Detect a Suspicious User-Agent String

This rule will create an alert if it sees a TCP connection with a user-agent string that contains “curl,” often used by attackers to launch attacks or perform reconnaissance.

```c
alert tcp any any -> any any (msg:"Suspicious User-Agent detected"; flow:to_server,established; content:"User-Agent|3a| "; nocase; content:"curl|2f|"; nocase; sid:1000002; rev:1;)
```

## 3\. A Rule to Detect a Specific Vulnerability in a Web Application

This rule will create an alert if it sees a TCP connection with a POST request to a web application’s “/login.php” page with a username and password parameter followed by a single quote, a common indicator of a SQL injection attempt.

```c
alert tcp anyany -> anyany (msg: "Possible SQL Injection attempt"; flow:to_server, established; content: "POST"; nocase; content:"/login.php"; nocase; content: "username="; nocase; content: "password="; nocase; content:"'"; sid:1000003; rev:1;)
```

## 4\. A Rule to Detect a Suspicious DNS Query

This rule will create an alert if it sees a UDP connection on port 53 (DNS) with a DNS query for the domain “example.com.” The rule uses the “depth” option to specify that it should only check the first six bytes of the DNS query, which are the standard DNS header fields.

```c
alert udp anyany -> any 53 (msg: "Suspicious DNS Query detected"; content:"|00 01 00 00 00 01|"; depth:6; content: "example.com"; nocase; sid:1000004; rev:1;)
```

## 5\. A Rule to Detect a Known Malware Signature in Network Traffic

This rule will create an alert if it sees a TCP connection with a payload with a specific sequence of bytes (in this case, the string “ZOOM”). This sequence is a known signature of the Zeus botnet malware used for financial fraud and other malicious activities.

```c
alert tcp any any -> any any (msg:"Possible Zeus Botnet C&C Traffic"; flow:established,to_server; content:"|5a 4f 4f 4d 00 00|"; depth:6; sid:1000005; rev:1;)
```

## Conclusion

In this guide, we explored the basics of Snort, from installation to writing and testing simple rules. By setting up rules to detect ICMP traffic and TCP connections to specific ports, we’ve seen how Snort can effectively monitor and alert on network activity. This powerful IDS/IPS tool allows you to craft rules tailored to your specific security needs, making it a flexible and essential part of network defense. Whether you’re securing a home lab or a corporate network, understanding how to configure and apply Snort rules is a valuable skill in protecting against potential threats.

---
