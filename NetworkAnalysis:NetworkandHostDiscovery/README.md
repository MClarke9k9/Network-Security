# In this section of Network and Security we will learn how to Identify the operating system of each host on the network and determine whether the host or its services are vulnerable to known exploits using Nmap.

---

## Step 1: Perform OS Detection on Each Host

Use a stealth scan with OS detection enabled.

### Scan Host 1

```bash
nmap -sP <your IP address>
```
<img width="669" height="173" alt="Screenshot 2026-02-22 at 8 46 54 PM" src="https://github.com/user-attachments/assets/fc686fcb-2247-4b66-b0c2-800ae96d5c93" />

**What the flags mean:**

* `-sS` → Stealth (SYN) scan (does not complete TCP handshake)
* `-O` → Attempts OS detection using fingerprinting
* `-Pn` → Skips ICMP ping (assumes host is online)

**Result:**
Nmap predicts the host is running **Linux (4.x or 5.x)**.


---

### Scan Host 2

```bash
sudo nmap -sS -O -Pn <your IP address>
```

**Result:**
Nmap predicts this host is also **Linux (likely 5.x)**.
<img width="801" height="361" alt="Screenshot 2026-02-22 at 8 49 16 PM" src="https://github.com/user-attachments/assets/342075f5-7262-46aa-b17f-601999c1378c" />

---

### Scan Host 3

```bash
 nmap -F <your IP address>
```

**Result:**
Nmap predicts this host is **Linux (3.x, 4.x, or 5.x)** with high confidence.
<img width="657" height="189" alt="Screenshot 2026-02-22 at 8 47 49 PM" src="https://github.com/user-attachments/assets/0daf7028-e0f5-4849-b563-2fafca86a825" />

---

## Why OS Detection Matters

Knowing a host runs Linux helps you:

* Understand file system structure
* Identify relevant kernel vulnerabilities
* Eliminate Windows-specific exploits
* Improve your network documentation

Add OS details to your network diagram.

---

## Step 2: Scan for Known Vulnerabilities

Use Nmap’s built-in vulnerability detection scripts.

---

### Check Host 1 for Vulnerabilities

```bash
nmap --script vuln <your IP address>
```

**What it does:**

* Runs the `vuln` script category
* Tests for 100+ known vulnerabilities

**Findings:**

* Vulnerable to:

  * `http-phpmyadmin-dir-traversal`
  * `http-vuln-cve2011-3192`
* Indicates the host is running the **Apache web server**
* Provides CVE references for further investigation

Add discovered vulnerabilities to your network diagram.

---

### Check Host 2 for Vulnerabilities

```bash
nmap --script vuln <your IP address>
```

**Findings:**

* No vulnerabilities reported
* Likely fewer open services (e.g., no port 80 open)

⚠️ Note: “No known vulnerabilities” does not mean the system is secure — only that none were detected by this script set.

---

### Check Host 3 for Vulnerabilities

```bash
nmap --script vuln <your IP address>
```

**Findings:**

* No vulnerabilities identified
* Scan may take longer depending on open ports
<img width="799" height="507" alt="Screenshot 2026-02-22 at 8 53 43 PM" src="https://github.com/user-attachments/assets/3587dfc1-44b6-4c1d-a3de-3df3a3524844" />

---

## Evasion & Stealth Considerations

Nmap scans can be detected by IDS/IPS systems.
To reduce detection risk:

* Use stealth scan (`-sS`)
* Limit scan speed
* Scan only common ports
* Use decoy techniques (advanced)

---

## Final Outcome

1. Identified the operating systems of multiple hosts
2. Determined they are likely Linux-based systems
3. Scanned for known vulnerabilities
4. Identified a vulnerable Apache web server on one host
5. Updated your network diagram with OS and vulnerability data

You now understand how **Nmap** can be used to:

* Enumerate hosts
* Detect operating systems
* Identify exploitable vulnerabilities
* Support network security assessments

