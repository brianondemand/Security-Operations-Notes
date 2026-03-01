# Simulating Attacks and Monitoring Them in Wazuh

##  Objective

Attack an Ubuntu machine from Kali and detect the activity inside the Wazuh Dashboard.

You will simulate:

1. Ping sweep
    
2. Nmap scan
    
3. SSH brute-force
    
4. File integrity tampering
    
5. Privilege escalation attempt
    

---

### LAB 1 — Ping Detection

**On Kali:**

```bash
ping 192.168.10.X
```

(Let X be Ubuntu’s IP)

Let it run for 15–20 seconds.

---

 **In Wazuh Dashboard:**

Go to:

Security Events → Search

Filter:

```
agent.name: Ubuntu
```

You should see:

- ICMP events
    
- Network scan related alerts
    

---

### LAB 2 — Nmap Scan Detection

**On Kali:**

```bash
nmap -sS -sV 192.168.10.X
```

Then try aggressive scan:

```bash
nmap -A 192.168.10.X
```

---

**In Wazuh Dashboard:**

Go to:

Threat Hunting

Search:

```
nmap
```

You should see alerts such as:

- Port scanning detected

- Multiple connection attempts

- Suspicious network activity


Wazuh detects this from log correlation and firewall logs.

---

### LAB 3 — SSH Brute Force Attack

**On Kali:**

```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.10.X
```

Or simpler:

```bash
hydra -l ubuntu -p wrongpassword ssh://192.168.10.X
```

Let it run for 30–60 seconds.

---

**In Wazuh Dashboard:**

Search:

```
sshd
```

or

```
authentication failure
```

You should see:

- Multiple failed login attempts

- Possible brute-force detection alert

- Rule level 10+ alerts


---

### LAB 4 — File Integrity Monitoring (FIM)

On Ubuntu (Target):

Edit a monitored file:

```bash
sudo nano /etc/passwd
```

(Add a space, save and exit)

---

**In Wazuh Dashboard:**

Go to:

Integrity Monitoring

You should see:

- File modified: /etc/passwd
    
- Hash change detected
    
- Severity alert
    

Wazuh monitors system file changes in real time.

---

### LAB 5 — Privilege Escalation Attempt

On Ubuntu:

Create a suspicious SUID file:

```bash
sudo chmod u+s /bin/bash
```

---

Then check alerts in Wazuh:

Search:

```
suid
```

You should see:

- Permission change detected

- Possible privilege escalation technique


---

### LAB 6 — Suspicious User Creation

On Ubuntu:

```bash
sudo useradd hackeruser
```

Check Wazuh:

Search:

```
new user
```

You should see:

- New user created alert

- Security audit log event


---

### Bonus — Generate Clear Alerts Fast

If alerts don’t show immediately:

On Ubuntu:

```bash
sudo systemctl restart wazuh-agent
```

On Wazuh server:

```bash
sudo systemctl restart wazuh-manager
```

Wait 10–20 seconds.

---

### Final Test for Your Students

Have them:

1. Perform an Nmap scan

2. Perform SSH brute force

3. Modify /etc/passwd

4. Create a new user


Then they must:

- Identify each attack in Wazuh

- Screenshot the alert

- State rule ID

- State severity level

- Explain what happened


---

If you want, I can now:

- Create a structured student practical exam version

- Or create an attacker vs defender scenario script

- Or help you tune Wazuh rules to make detection stronger


---