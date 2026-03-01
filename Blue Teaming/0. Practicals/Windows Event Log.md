Windows event log is an in-depth record of events related to the system, security, and application stored on a Windows operating system.  Event logs can be used to track system and some application issues and forecast future problems.

### The Elements of a Windows Event Log

Windows event log provides information about hardware and software events occurring on a Windows operating system. It helps network administrators track potential threats and problems potentially degrading performance. Windows stores event logs in a standard format allowing a clear understanding of the information. Following are the main elements of an event log:

- **Log name:** Name of the event log to which events from different logging components will be written. Events are commonly logged for system, security, and applications.
  
- **Event date/time:** Includes the date and time when the event occurred.
  
- **Task category:** Identifies the type of recorded event log. Application developers can also define task categories to serve as extra information about the event. 
  
- **Event ID:** This Windows identification number helps network administrators uniquely identify a specific logged event. 
  
- **Source:** Name of the program or software causing the event log.
  
- **Level:** Event level represents the severity of the recorded event log. These include information, error, verbose, warning, and critical.
  
- **User:** Name of the user who logged onto the Windows computer when the event occurred.
  
- **Computer:** Name of the computer logging the event.

---

### What Types of Information Are Stored in Windows Event Log?

Windows event logs store information about different events that occur within the system. The type of information stored varies based on the category of an event log. Data is recorded commonly for four Windows event log types:

- system
- application
- setup
- security

Windows system event log includes information about incidents related to the Windows operating system. Similarly, the application event log provides some information about errors occurring within the installed software on the machine. The security event log contains data about security events on the system, while the setup log focuses more on installation-related events. The information stored in event logs allows system administrators to investigate different problems and diagnose them accordingly.

---

### Common Event Log Categories and Types

Event logs are classified into four categories such as application, security, setup, and system. There's also a special category of event logs called forwarded events.

- **System Log:** Windows system event log contains events related to the system and its components. Failure to load the boot-start driver is an example of a system-level event.
  
- **Application Log:** Events related to a software or an application hosted on a Windows computer get logged under the application event log. For example, the problem in starting Microsoft PowerPoint comes under the Application log.
  
- **Security:** Security logs contain events related to the safety of the system. The event gets recorded via the Windows auditing process. Examples include failed and valid logins, file deletions, etc.
  
- **Setup:** The setup log contains events that occur during the installation of the Windows operating system. On domain controllers, this log will also record events related to Active Directory.
  
- **Forwarded Events:** Contains event logs forwarded from other computers in the same network.

Windows events are further divided into five different types:

- **Information:** Indicates an application or service is operating well. For example, when Windows loads the network driver, the incident will be logged as an information event.
  
- **Warning:** Unimportant events hinting toward potential issues in the future. A warning event will get logged for a problem like low disk space.
  
- **Error:** Describes a significant issue when a system cannot function normally—for example, the operating system stops responding.
  
- **Success Audit:** Records valid attempt of audited security access for security log. For example, a login attempt that goes well will come under a success audit event.
  
- **Failure Audit:** Indicates the failure of audited security access under the security log, such as the inability to access the network drive.

---

### How to Check and View Windows Event Logs

Windows event log location is **C:\WINDOWS\system32\config\ folder**. Event logs can be checked with the help of 'Event Viewer' to keep track of issues in the system. Here's how:

- Press the **Windows key + R** on your keyboard to open the run window
- In the run dialog box, type in **eventvwr** and click OK
- In the Event Viewer window, expand the **Windows Logs** menu
- Under the **Windows Logs** menu, you'll notice different categories of event logs—application, security, setup, system, and forwarded events
- Click on one of the event logs to check and view the events recorded under it

---

### Importance of Windows Event Log Monitoring

Event log monitoring helps system and network engineers stay updated about errors, unauthorized activity, external threats, system failures, and other important problems occurring inside a system. 

Windows event logging provides detailed information like source, username, computer, type of event, level, etc., which helps effectively diagnose and fix issues affecting the system. It also allows network engineers to predict future problems based on the data provided by event logs.

There are automated event log monitoring tools designed to help system engineers avoid the manual process of going through event logs. 

These tools come with an intuitive web console to help filter and view event logs showing a critical issue in the system. In addition to Windows events, these tools help with the analysis of SNMP traps and syslog messages. 

System engineers can forward event logs as syslog messages to perform log management activities using an event log monitoring tool. 

Moreover, some tools help with regulatory compliance via automated log archival and cleanup. Engineers can also set alerts for fast troubleshooting based on the event's time, type, and source.