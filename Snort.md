
SNORT is an **open-source, rule-based** Network Intrusion Detection and Prevention System **(NIDS/NIPS)**. It was developed and still maintained by Martin Roesch, open-source contributors, and the Cisco Talos team.

In this blog we are going to see working of snort as IDS and IPS. Lets dive in the topic.

## Snort comes with fallowing capabilities:

- Live traffic analysis
- Attack and probe detection
- Packet logging
- Protocol analysis
- Real-time alerting
- Modules & plugins
- Pre-processors
- Cross-platform support! (Linux & Windows)

##  Snort can be used in 3 modes:

- **Sniffer Mode —** Read IP packets and prompt them in the console application.
- **Packet Logger Mode —** Log all IP packets (inbound and outbound) that visit the network.
- **NIDS (Network Intrusion Detection System) and NIPS (Network Intrusion Prevention System) Modes —** Log/drop the packets that are deemed as malicious according to the user-defined rules.

##  Understanding of IDS/IPS

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

### Operation Mode 4: PCAP Investigation

Packet capture (PCAP) is a networking practice involving the interception of data packets travelling over a network. Once the packets are captured, they can be stored by IT teams for further analysis. The inspection of these packets allows IT teams to identify issues and solve network problems affecting daily operations.[*https://www.solarwinds.com/resources/it-glossary/pcap*](https://www.solarwinds.com/resources/it-glossary/pcap)

## Investigate PCAPs with Snort

Capabilities of Snort are not limited to sniffing, logging and detecting/preventing the threats. PCAP read/investigate mode helps you work with pcap files. Once you have a pcap file and process it with Snort, you will receive default traffic statistics with alerts depending on your ruleset.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*ZjJu6xqJSBCQWY01.png)

### Investigating single PCAP with parameter “-r”

Test the default reading option with pcap

```c
snort -r icmp-test.pcap
```

Investigate the pcap with our configuration file

```c
sudo snort -c /etc/snort/snort.conf -q -r icmp-test.pcap -A console -n 10
```

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*Cl-H3y98SAqhz4hd.png)

Our ICMP rule got a hit! As you can see in the given output, snort identified the traffic and prompted the alerts according to our ruleset.

### Investigating multiple PCAPs with parameter “ — pcap-list”

Investigate multiple pcaps with our configuration file

```c
sudo snort -c /etc/snort/snort.conf -q --pcap-list="icmp-test.pcap http2.pcap" -A console -n 10
```

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*7GxK3NXNwv2dNNiK.png)

snort identified the traffic and prompted the alerts according to our ruleset.

### Investigating multiple PCAPs with parameter “ — pcap-show”

Investigate multiple pcaps, distinguish each one

```c
sudo snort -c /etc/snort/snort.conf -q --pcap-list="icmp-test.pcap http2.pcap" -A console --pcap-show
```

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*PjeAaSrwVPkIom64.png)

## Snort Rule Structure

### Primary structure of the snort rule

The primary structure of the snort rule is shown below:

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*gARfxRRcwgIE70do.png)

Each rule should have a type of action, protocol, source and destination IP, source and destination port and an option.

Snort is in passive mode by default. So most of the time, you will use Snort as an IDS. You will need to start **“inline mode” to turn on IPS mode.**

Rules cannot be processed without a header. Rule options are “optional” parts.

almost impossible to detect sophisticated attacks without using the rule options.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*eA7VmlaobTc_O8m8.png)

### IP and Port Numbers

These parameters identify the source and destination IP addresses and associated port numbers filtered for the rule.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*_9EQ6qJFpHClc4Bd.png)

- **IP Filtering**

alert icmp 192.168.1.56 any <> any any (msg: “ICMP Packet From “; sid: 100001; rev:1;)

This rule will create an alert for each ICMP packet originating from the 192.168.1.56 IP address.

- **Filter an IP range**

alert icmp 192.168.1.0/24 any <> any any (msg: “ICMP Packet Found”; sid: 100001; rev:1;)

This rule will create an alert for each ICMP packet originating from the 192.168.1.0/24 subnet.

- **Filter multiple IP ranges**

alert icmp \[192.168.1.0/24, 10.1.1.0/24\] any <> any any (msg: “ICMP Packet Found”; sid: 100001; rev:1;)

This rule will create an alert for each ICMP packet originating from the 192.168.1.0/24 and 10.1.1.0/24 subnets.

- **Exclude IP addresses/ranges**

“negation operator” is used for excluding specific addresses and ports. Negation operator is indicated with “!”

alert icmp!192.168.1.0/24 any <> any any (msg: “ICMP Packet Found”; sid: 100001; rev:1;)

This rule will create an alert for each ICMP packet not originating from the 192.168.1.0/24 subnet.

- **Port Filtering**

alert tcp any any <> any 21 (msg: “FTP Port 21 Command Activity Detected”; sid: 100001; rev:1;)

This rule will create an alert for each TCP packet sent to port 21.

- **Exclude a specific port**

alert tcp any any <> any!21 (msg: “Traffic Activity Without FTP Port 21 Command Channel”; sid: 100001; rev:1;)

This rule will create an alert for each TCP packet not sent to port 21.

- **Filter a port range (Type 1)**

alert tcp any any <> any 1:1024 (msg: “TCP 1–1024 System Port Activity”; sid: 100001; rev:1;)

This rule will create an alert for each TCP packet sent to ports between 1–1024.

- **Filter a port range (Type 2)**

alert tcp any any <> any:1024 (msg: “TCP 0–1024 System Port Activity”; sid: 100001; rev:1;)

This rule will create an alert for each TCP packet sent to ports less than or equal to 1024.

- **Filter a port range (Type 3)**

alert tcp any any <> any 1025: (msg: “TCP Non-System Port Activity”; sid: 100001; rev:1;)

This rule will create an alert for each TCP packet sent to source port higher than or equal to 1025.

- **Filter a port range (Type 4)**

alert tcp any any <> any \[21,23\] (msg: “FTP and Telnet Port 21–23 Activity Detected”; sid: 100001; rev:1;)

This rule will create an alert for each TCP packet sent to port 21 and 23.

### Direction

The direction operator indicates the traffic flow to be filtered by Snort. The left side of the rule shows the source, and the right side shows the destination.

- **\->** Source to destination flow.
- **<>** Bidirectional flow

**Note that there is no “<-” operator in Snort.**

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*SaOPbOJmvPcn278b.png)

### There are three main rule options in Snort;

- **General Rule Options —** Fundamental rule options for Snort.
- **Payload Rule Options —** Rule options that help to investigate the payload data. These options are helpful to detect specific payload patterns.
- **Non-Payload Rule Options —** Rule options that focus on non-payload data. These options will help create specific patterns and identify network issues.

### General Rule Options

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*EEdTDI_xzReYjoMC.png)

### Payload Detection Rule Options

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*uoXYyqLHv5uHgEZx.png)

### Non-Payload Detection Rule Options

There are rule options that focus on non-payload data. These options will help create specific patterns and identify network issues.

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*3peXm_btlWwxMbFO.png)

Remember, once you create a rule, it is a local rule and should be in your “local.rules” file. This file is located under “/etc/snort/rules/local.rules”. A quick reminder on how to edit your local rules is shown below.

##  Snort2 Operation Logic: Points to Remember

### Points to Remember

**Main Components of Snort**

- **Packet Decoder —** Packet collector component of Snort. It collects and prepares the packets for pre-processing.
- **Pre-processors —** A component that arranges and modifies the packets for the detection engine.
- **Detection Engine —** The primary component that process, dissect and analyse the packets by applying the rules.
- **Logging and Alerting —** Log and alert generation component.
- **Outputs and Plugins —** Output integration modules (i.e. alerts to syslog/mysql) and additional plugin (rule management detection plugins) support is done with this component.

### There are three types of rules available for snort

- **Community Rules —** Free ruleset under the GPLv2. Publicly accessible, no need for registration.
- **Registered Rules —** Free ruleset (requires registration). This ruleset contains subscriber rules with 30 days delay.
- **Subscriber Rules (Paid) —** Paid ruleset (requires subscription). This ruleset is the main ruleset and is updated twice a week (Tuesdays and Thursdays).

**Note:** Once you install Snort2, it automatically creates the required directories and files. However, if you want to use the community or the paid rules, you need to indicate each rule in the **snort.conf** file.

- **snort.conf:** *Main configuration file.*
- **local.rules:***User-generated rules file.*

**Let’s start with overviewing the main configuration file (snort.conf)**

```c
sudo gedit /etc/snort/snort.conf
```
![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*W2sQMlXkbFpOm-SZ.png)

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*PaV0vDXsZXFkKbmL.png)

Data Acquisition Modules (DAQ) are specific libraries used for packet I/O, bringing flexibility to process packets. It is possible to select DAQ type and mode for different purposes.

There are six DAQ modules available in Snort;

- **Pcap:** Default mode, known as Sniffer mode.
- **Afpacket:** Inline mode, known as IPS mode.
- **Ipq:** Inline mode on Linux by using Netfilter. It replaces the snort\_inline patch.
- **Nfq:** Inline mode on Linux.
- **Ipfw:** Inline on OpenBSD and FreeBSD by using divert sockets, with the pf and ipfw firewalls.
- **Dump:** Testing mode of inline and normalisation.

The most popular modes are the default (pcap) and inline/IPS (Afpacket)

![](https://miro.medium.com/v2/resize:fit:640/format:webp/0*v_ZP3dTN-NxqChab.png)

---