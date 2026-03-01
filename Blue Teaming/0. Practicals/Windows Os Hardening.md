



Hardening is the process of reducing the attack surface of a system by disabling unnecessary services, enforcing least privilege, and ensuring secure configurations. For Windows 10, this involves a mix of operating system configuration, account management, patching, monitoring, and policy enforcement.

---

### 1. Keep the System Updated

Patching is the first line of defense. Microsoft releases regular security updates to fix vulnerabilities that attackers actively exploit. An unpatched system can be compromised in seconds by malware or exploits like EternalBlue (used in WannaCry). Windows Update should be configured for automatic installation, and students should understand the difference between feature updates (which change the OS version) and quality/security updates (which patch vulnerabilities).

---

### 2. User Account Control (UAC) and Least Privilege

Windows 10 users often run with administrator rights, which is dangerous. The principle of least privilege means accounts should only have the permissions they absolutely need. Normal users should operate as standard accounts, and elevation to administrator should require UAC confirmation. This prevents malware from making system-wide changes without the user realizing it.

---

### 3. Password and Authentication Policies

Weak passwords are still one of the most common attack vectors. Enforcing complex passwords, minimum length (ideally 12+ characters), and regular expiration can reduce risks. However, modern security best practice shifts toward encouraging **passphrases** and **multi-factor authentication (MFA)** instead of constant password changes. MFA, such as a hardware key or authenticator app, makes it much harder for attackers to log in even if passwords are stolen.

---

### 4. Windows Defender and Endpoint Protection

Windows 10 comes with Windows Defender Antivirus, which integrates with the OS to provide real-time scanning, cloud-based protection, and behavior monitoring. It should always be enabled unless replaced by an enterprise-grade EDR (Endpoint Detection and Response) tool. Defender’s controlled folder access feature can prevent ransomware from encrypting files by blocking unauthorized access. Teaching students how to tune exclusions and monitor Defender logs is essential.

---

### 5. Firewall and Network Protection

The Windows Defender Firewall is often disabled by users, but it’s a critical defense. It filters inbound and outbound traffic, blocking connections that don’t match defined rules. On top of that, Windows includes advanced features like IPsec for secure communication. In a hardening lab, students can configure rules to block unnecessary ports and limit which apps can reach the internet.

---

### 6. Application Control and Whitelisting

One of the strongest defenses against malware is preventing unauthorized applications from running. Windows 10 provides several tools:

* **AppLocker** allows admins to create rules about which executables, scripts, and installers are allowed.
* **Windows Defender Application Control (WDAC)** offers kernel-level enforcement, ensuring only trusted code runs.

For students, demonstrating the difference between blacklisting (blocking known-bad apps) and whitelisting (allowing only known-good apps) shows why whitelisting is more secure.

---

### 7. BitLocker Drive Encryption

Physical attacks are often overlooked. If a laptop is stolen, unencrypted disks give attackers access to sensitive data. BitLocker encrypts the entire drive, requiring a TPM (Trusted Platform Module) and/or PIN to unlock. Students should learn how to enable BitLocker, store recovery keys securely, and understand its role in preventing offline attacks.

---

### 8. Disable Unnecessary Services and Features

Every running service is a potential attack vector. For example, services like Remote Desktop Protocol (RDP) are commonly abused by attackers. If RDP isn’t needed, it should be disabled. If it is needed, it must be secured with MFA, Network Level Authentication, and restricted firewall rules. Similarly, features like SMBv1 (an old file-sharing protocol exploited by WannaCry) should be disabled.

---

### 9. Event Logging and Auditing

Blue teams need visibility. Windows 10 has powerful logging capabilities that, when configured correctly, can reveal brute-force attempts, privilege escalations, and suspicious process creations. Event logs should be forwarded to a SIEM (like Splunk or Wazuh) for centralized monitoring. Students should practice enabling advanced auditing policies and analyzing logs for early warning signs of compromise.

---

### 10. Security Baselines and Group Policy

Microsoft provides **Security Baselines** for Windows 10, which are preconfigured Group Policy settings aligned with best practices (like disabling guest accounts, enforcing strong crypto, etc.). In enterprise networks, Group Policy allows consistent enforcement across all endpoints. In a lab, students can apply a baseline and then test which settings are enforced, teaching them how policy compliance strengthens defense.

---

### 11. Browser and Application Hardening

Since web browsers are the most common entry point for attacks, hardening them is critical. Students should learn to:

* Disable insecure plugins and extensions.
* Use Microsoft Edge with SmartScreen enabled.
* Enforce sandboxing and site isolation.
* Disable macros in Office documents unless digitally signed.

---

### 12. Monitoring and Threat Hunting

Finally, hardening isn’t just about prevention but also about detection. Tools like Sysmon (from Sysinternals) extend logging beyond what Windows normally provides, capturing process creation, network connections, and driver loading. By teaching students how to deploy Sysmon and analyze logs, you’re training them to detect persistence, privilege escalation, and lateral movement attempts in real time.


---


### A. **Patch and Update**

- Run **Windows Update** and install latest security updates.
    
- Discuss CVEs patched by recent updates.
    

### B. **User Accounts & UAC**

- Create a **standard user account** for daily use.
    
- Remove admin rights from non-admin accounts.
    
- Demonstrate UAC prompts when elevation is required.
    

### C. **Password & Authentication Policies**

- Use `gpedit.msc` → Security Settings → Account Policies:
    
    - Minimum length: 12+
        
    - Password history: 10 passwords
        
    - Lockout threshold: 5 invalid attempts
        
- Enable **Windows Hello / MFA** if supported.
    

### D. **Windows Defender & Endpoint Protection**

- Re-enable Windows Defender with real-time protection.
    
- Configure **Controlled Folder Access** to prevent ransomware.
    

### E. **Firewall Configuration**

- Enable Windows Defender Firewall.
    
- Block inbound RDP except from a trusted IP.
    
- Block unnecessary outbound connections.
    

### F. **Disable Unnecessary Services**

- Disable SMBv1:
    
    `Disable-WindowsOptionalFeature -Online -FeatureName smb1protocol`
    
- Disable guest accounts and restrict RDP access.
    

### G. **BitLocker Drive Encryption**

- Enable BitLocker for system drive.
    
- Save recovery keys securely.
    

### H. **Application Control**

- Configure AppLocker to block execution from `C:\Users\*\Downloads\`.
    
- Only allow signed executables.
    

### I. **Logging and Auditing**

- Enable Advanced Audit Policies:
    
    - Logon/Logoff events
        
    - Account lockouts
        
    - Process creation (`4688`)
        
- Install **Sysmon** to capture process/network events.
    
- Forward logs to SIEM (optional).