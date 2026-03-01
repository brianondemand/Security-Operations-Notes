
## 🎯 Learning Objectives

By the end of this module, students will be able to:

- Harden Windows and Linux systems against common privilege escalation and persistence techniques.
    
- Apply patch and configuration management principles to reduce attack surface.
    
- Deploy and use EDR, host-based firewalls, and logging tools.
    
- Monitor and analyze logs to detect malicious activity.
    
- Implement user and privilege management to stop lateral movement.
    

---

## **Lab 1 – Windows System Hardening & Monitoring**

**Attack:**

- Red team simulates credential theft with **Mimikatz**.
    
- Try creating persistence with a **Scheduled Task** or **Registry Run Key**.
    

**Defense:**

- Configure **Sysmon** and forward logs to SIEM (e.g., Splunk/ELK/Wazuh).
    
- Enable PowerShell logging and script block logging.
    
- Use Group Policy to:
    
    - Enforce password complexity.
        
    - Disable unneeded services.
        
    - Limit local admin accounts.
        
- Verify detection by reviewing logs for suspicious process creation (`lsass.exe`, scheduled tasks, registry writes).
    

**Key Teaching Point:** Students see exactly how Mimikatz looks in logs and how Sysmon/PowerShell logging helps detect it.

---

## **Lab 2 – Linux Hardening & Monitoring**

**Attack:**

- Red team escalates privileges using **SUID binary exploitation** or misconfigured `sudo`.
    
- Attempt SSH brute force using Hydra.
    

**Defense:**

- Disable root SSH login, enforce key-based auth.
    
- Configure **auditd** rules to monitor file access and privilege escalation attempts. Example rule:
    
    ```bash
    -w /etc/sudoers -p wa -k privilege
    ```
    
- Use `fail2ban` to block repeated failed SSH logins.
    
- Harden SSH (`/etc/ssh/sshd_config` → PermitRootLogin no, PasswordAuthentication no).
    

**Key Teaching Point:** Students see failed brute force attempts blocked by fail2ban and privilege escalations logged by auditd.

---

## **Lab 3 – Patch & Configuration Management**

**Attack:**

- Red team exploits an unpatched vulnerability (e.g., **EternalBlue MS17-010** on Windows or an outdated Apache server).
    

**Defense:**

- Demonstrate **patching cycle**: scanning with Nessus/OpenVAS → applying patch → rescanning.
    
- Use a configuration management tool (e.g., Ansible or Windows WSUS) to enforce secure baselines across multiple hosts.
    
- Compare vulnerability scan reports before and after patching.
    

**Key Teaching Point:** Shows how patch management directly reduces exploitable attack surface.

---

## **Lab 4 – Endpoint Security (EDR & Host Firewall)**

**Attack:**

- Red team deploys a reverse shell payload.
    
- Attempt lateral movement via SMB (Pass-the-Hash or psexec).
    

**Defense:**

- Deploy an EDR tool (open-source: **Wazuh, Velociraptor, OSSEC**).
    
- Configure host firewall rules to block unauthorized inbound connections.
    
- Analyze EDR alerts: suspicious process spawning (`powershell.exe` → base64 encoded command).
    

**Key Teaching Point:** EDR provides visibility beyond AV, catching fileless or “living-off-the-land” attacks.

---

## **Lab 5 – Privilege & User Management**

**Attack:**

- Red team attempts **Pass-the-Hash** or **Kerberoasting** in a Windows domain.
    
- On Linux, try privilege escalation by abusing `sudo` or writable cron jobs.
    

**Defense:**

- Enforce **Least Privilege**: remove unnecessary local admin rights.
    
- Configure **Microsoft LAPS** to randomize local admin passwords.
    
- Enforce MFA for domain accounts.
    
- Monitor authentication logs for anomalous login attempts.
    

**Key Teaching Point:** Privilege reduction makes lateral movement much harder for attackers.

---

## **Lab 6 – System Logging & Detection Engineering**

**Attack:**

- Red team simulates persistence via creating a new user account or modifying scheduled tasks.
    
- Try data exfiltration via PowerShell (compress & upload files).
    

**Defense:**

- Collect logs:
    
    - Windows: Security Event Logs + Sysmon.
        
    - Linux: auth.log, auditd.
        
- Ingest logs into SIEM.
    
- Write detection rules:
    
    - Detect new local accounts (`Event ID 4720`).
        
    - Detect unusual process execution (`cmd.exe` spawning PowerShell).
        
    - Detect excessive outbound traffic (data exfil).
        

**Key Teaching Point:** Blue teams aren’t just monitoring — they must **engineer detections** tailored to attacker TTPs.

---

## **Capstone Challenge – Attack vs Defense**

- Red team executes a mini-attack chain:
    
    - Brute force SSH → gain foothold → privilege escalate → persistence → data exfiltration.
        
- Blue team’s job:
    
    - Detect every stage in SIEM.
        
    - Contain compromised host (isolate VM).
        
    - Eradicate persistence.
        
    - Document incident with timeline and lessons learned.
        

**Key Teaching Point:** Students see how **all system security layers work together** in real-world defense.

---

## Tools & Setup

- Virtual Lab (VMs on VirtualBox/VMware/Proxmox or cloud-based ranges).
    
- Windows 10/11 VM + Server 2019 for AD labs.
    
- Ubuntu/CentOS VM for Linux labs.
    
- Tools: Sysmon, Splunk/ELK/Wazuh, Auditd, Fail2ban, Nessus/OpenVAS, Hydra, Mimikatz, Velociraptor.
    

---

👉 This structure allows you to run **6 labs + 1 capstone project**. Each lab starts with a red team technique (students already know this) and follows with hardening, logging, and detection (blue team skill-building).

Would you like me to **expand these into a full 4-week module plan** (with weekly objectives, lecture notes, lab steps, and assignments), so you can deliver it as a structured mini-course?