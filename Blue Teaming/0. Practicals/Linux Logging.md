Operating system logs provide a wealth of diagnostic information about your computers, and Linux is no exception. Everything from kernel events to user actions is logged by Linux, allowing you to see almost any action performed on your servers. In this guide, we’ll explain what Linux logs are, where they’re located, and how to interpret them.

The logging framework for Linux includes a set of directories, files, services, and commands that administrators can use. As a Linux system administrator, knowing your way around the Linux log locations, commands, and configuration will be essential for troubleshooting issues on the systems or applications you administer.

### Linux System Logs

Linux has a special directory for storing logs called `/var/log`. This directory contains logs from the OS itself, services, and various applications running on the system. Here’s what this directory looks like on a typical Ubuntu system.

![Linux system log terminal](https://www.loggly.com/wp-content/uploads/2015/05/Linux-system-log-terminal.png)

Some of the most important Linux system logs include:

- `/var/log/syslog` and `/var/log/messages` store all global system activity data, including startup messages. Debian-based systems like Ubuntu store this in /`var/log/syslog`, while Red Hat-based systems like `RHEL` or `CentOS` use `/var/log/messages`.
  
- `/var/log/auth.log` and `/var/log/secure` store all security-related events such as logins, root user actions, and output from pluggable authentication modules (PAM). Ubuntu and Debian use `/var/log/auth.log`, while Red Hat and CentOS use `/var/log/secure`.
  
- `/var/log/kern.log` stores kernel events, errors, and warning logs, which are particularly helpful for troubleshooting custom kernels.
  
- `/var/log/cron` stores information about scheduled tasks (cron jobs). Use this data to verify your cron jobs are running successfully.

- `/var/log/wtmp`: This file contains a history of all user login and logout activity for auditing users' activity on the system.

- `/var/log/lastlog`: Similar to the wtmp audit file, this log file tracks users' last logins. This is a binary file you can read via the lastlog command.

Some applications also write log files to this directory. For example, the Apache web server writes logs to the /var/log/apache2 directory (on Debian), while MySQL writes logs to the /var/log/mysql directory. Some applications also log via Syslog.

---
## Introduction to Syslog

Syslog is a network-based logging protocol that monitors your systems and applications. This protocol provides a standard way for services and applications to report their logs. That way, they can be processed and redirected as needed.

### Standardized message format

The syslog protocol provides a message format defined by the `RFC 5424` standard. (This document describes the syslog protocol, which is used to convey event notification messages. This protocol utilizes a layered architecture.)

In this format, common event information is defined, such as the timestamp, hostname, and the name of the application that produced the message. 

To further support the structuring of this message, syslog facilities are available to denote which part of the system the log comes from. 

This is done by attaching a number to the message. Below is a list of all available facilities, numbered from 0 to 23:

|**Facilities Code**|**Keyword**|**Description**|
|---|---|---|
|0|kern|Kernel messages|
|1|user|User-level messages|
|2|mail|Mail system|
|3|daemon|System daemons|
|4|auth|Security/authorization messages|
|5|syslog|Messages generated internally by syslogd|
|6|lpr|Line printer subsystem|
|7|news|Network news subsystem|
|8|uucp|UUCP subsystem|
|9|cron|Clock daemon|
|10|authpriv|Security/authentication messages|
|11|ftp|FTP daemon|
|12|ntp|NTP subsystem|
|13|security|Log audit|
|14|console|Log alert|
|15|clock|Clock daemon|
|16-23|local0 - local7|Locally used facilities|

Similarly, priority can be attached to a message using a number between 0 and 7.

|**Facilities Code**|**Keyword**|**Description**|
|---|---|---|
|0|emergency|System is unusable|
|1|alert|Action must be taken immediately|
|2|critical|Critical conditions|
|3|error|Error Conditions|
|4|warning|Warning conditions|
|5|notice|Normal but significant condition|

By using both the facilities and priorities in the syslog message, tools that access the syslog data can now filter messages based on the originating facility and the severity of messages. We’ll see an example of this in the next section.

### Syslog Protocol Implementations

The syslog _process_ runs as a daemon on the system to receive, store, and interpret syslog messages from other services or applications. That service typically listens on `port 514 for TCP` and` 601 for UDP` connections. Many applications allow you to configure their event logging to push messages to a running syslog service.

The syslog protocol is also implemented by different services like [rsyslog](https://www.rsyslog.com/) and [syslog-ng](https://www.syslog-ng.com/), allowing you to choose a service based on the feature set you need. Because these services have aligned to the syslog protocol, they are interchangeable for system and application logging, making them very scalable.

---

#### The Rsyslog Daemon

Rsyslog is a modern, open-source implementation of the syslog daemon, offering a high-performance, security-focused, modular design for any environment. The rsyslog daemon runs as a service on your host, listening for log messages sent to it and routing those messages based on defined actions.

In a typical installation of rsyslog, the daemon is configured through a file located at `/etc/rsyslog.conf`. In this config file, using selectors for the facilities and priority of the log message allows you to define what action should be carried out for the message. 

In the following example, any messages with the facility of mail and a priority of notice or higher will be written to a log file located at `/var/log/mail_errors`.

---

## Basic Commands for Linux Logging

As an administrator of Linux servers, you will often connect to these servers to read log messages for troubleshooting systems or the services running on them. Several utility commands are available on Linux systems, simplifying how you navigate stored log messages. The following section outlines some basic log commands available:

- `cat`: Short for concatenate, which allows you to view the contents of one or more files in the terminal.

- `more`: Similar to cat utility, this command reads the content of files in the terminal. However, this utility will interactively display it one page at a time to the user for an easier manual reading experience.

- `less`: Much like the more utility, this command displays a single terminal screen of content at a time, allowing for easier navigation of large text files.

- `tail`: By default, tail displays the last ten lines written to a file. Using the follow option (-f or --follow) allows you to monitor the file continuously. As new lines are written, they are printed to the user's terminal.

- `head`: This utility is the opposite of the tail command, fetching the beginning lines of a file. By default, head will display the first ten lines of a file.

- `grep`: This command allows you to parse input text using filters and regex to find specific patterns in the text. It is useful for searching and manipulating text in scripts or automation.


With these basic commands, you can easily access and navigate the log messages on your system. By using pipes `(|)` in your commands, you can chain multiple commands together, filtering their outputs even further. 

For example, the following chain of commands will read the contents of the `/var/log/cron` file and check if any message contains the string foo.

```bash
cat /var/log/cron | grep "foo"
```

Advanced logging operations can also be done with other commands like `awk`, `cut`, and advanced `grep` filters, allowing you to gain more insight into what happens on your system.