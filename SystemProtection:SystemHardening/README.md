# Hardening Linux Systems

## In this lesson, you will harden an Ubuntu Server (the **Linux Console**) so it functions strictly as a **web server**. You will:
- Baseline the server using **Nmap**
- Prevent **root** SSH logins
- Disable and remove unnecessary services (example: **LDAP**)
- Enable and configure the firewall (**UFW**) to allow only required services

## Step 1: Baseline the Linux Console with Nmap

1. Connect to the **NMAP Console** machine.
2. Scan the Linux Console to identify open ports:
   ```bash
   nmap <your IP>
   ```

   Expected baseline ports (example):

22 (SSH) — required for admin management

80 (HTTP) — web traffic

443 (HTTPS) — encrypted web traffic

389 (LDAP) — not required for a web server (remove)

## Step 2: Disable Root SSH Login

1. Switch to the Linux Console.

2. Open the SSH configuration file:

```bash
sudo vi /etc/ssh/sshd_config
```
3. Find the line:
```bash
PermitRootLogin yes
```
Change it to:

```Plain text
PermitRootLogin no
```
Save and exit (```Esc```, then ```:wq```).

Restart SSH to apply changes:

```
sudo systemctl restart sshd
```
### Result: Admins can no longer SSH directly as root (they must use a normal account + sudo).
---

## Step 3: Identify Unnecessary Services

1. View running services:

```
sudo systemctl status
```
2. Look for services that are not required for a web server (example: LDAP service ```slapd.service```).

3. Press ```Ctrl + C``` to return to the command line.
---

## Step 4: Disable and Remove LDAP (slapd)

1. Disable the service immediately and prevent it from starting
```
sudo systemctl disable --now slapd.service
```
2. Identify related files/packages
```
dpkg -S slapd
```
3.Remove the package and dependencies
```
sudo apt purge slapd
```
Type Y when prompted.

4. Confirm removal
```
dpkg -S slapd
```
### Result: You should see output indicating no paths match slapd.
---

## Step 5: Configure and Enable the Firewall (UFW)

1. Check firewall status:
```
sudo ufw status
```
If inactive, proceed.

2. Allow required web services (HTTP + HTTPS):
```
sudo ufw allow "Apache Full"
```
3. Allow SSH for administration:
```
sudo ufw allow OpenSSH
```
4. Enable the firewall:
```
sudo ufw enable
```
5. Verify:
```
sudo ufw status
```
### Result: Firewall is active, allowing only Apache and OpenSSH.
---

## Step 6: Re-scan with Nmap to Verify Hardening

1. Return to the NMAP Console.

2. Scan again:
```
nmap <your IP>
```
Confirm:

389/tcp (ldap) is closed

Web ports (80, 443) and SSH (22) remain open as needed

## Part 2: Hardening Windows Systems (DNS Server)
## Overview

This section hardens a Windows Server so it functions strictly as a DNS Server. Unnecessary roles, services, and accounts are removed or disabled.

All IP addresses are represented as <your IP>.
---
## Step 1: Baseline the Windows Server with Nmap

On the NMAP Console, run:
```
nmap -Pn <your IP>
```
Review open ports:

53 (DNS) – required
80 (HTTP) – not required and should be removed
135, 3389 – required for this environment
---
## Step 2: Remove IIS (Web Server Role)

1. Log in to the Windows Server.
2. Open Server Manager.
3. Select Manage → Remove Roles and Features.
4. Click Next until reaching Server Roles.
5. Uncheck Web Server (IIS).
6.Continue through the wizard and allow the server to restart.

### Result: Port 80 is no longer required or active.
---

## Step 3: Harden the Administrator Account

1. Open Server Manager → Tools → Computer Management.
2. Navigate to System Tools → Local Users and Groups → Groups.
3. Open the Administrators group to review members.

Rename the Default Administrator Account

1. Go to Users.
2. Right-click Administrator → Rename.
3. Rename the account to:
```
psadmin
```
Set a Password for the Renamed Account

1. Right-click psadmin → Set Password → Proceed.
2. Set the password:
```
Somepassword
```
---

### Step 4: Disable Unnecessary Accounts

1. In Users, open WDAGUtilityAccount.
2. Check Account is disabled.
3. Click OK.

### Result: Only required administrative accounts remain active.
---

## Step 5: Disable Unnecessary Services

1. Open Services from the Start menu.
2. Disable services not required for DNS functionality:
    - ActiveX Installer
    - Downloaded Maps Manager
    - Print Spooler
3. For each service:
    - Double-click the service
    - Set Startup type to Disabled
    - Click OK

## Step 6: Configure AppLocker

1. In Services, locate Application Identity.
2. Set Startup type to Automatic and start the service if possible.
3. Open Local Security Policy.
4. Navigate to:
```
Application Control Policies → AppLocker
```
5. Right-click Executable Rules → Automatically Generate Rules.
6. Name the rule set:
```
Default Programs
```
7. Complete the rule generation wizard.
---

## Step 7: Block Wireshark (Test AppLocker Deny Rule)

1. Open and close Wireshark once to confirm it works.
2. In Executable Rules, locate the Wireshark rule.
3. Open the rule and change Action to Deny.
4. Attempt to open Wireshark again.

### Result: The application is blocked with an administrator warning.
---

## Step 8: Verify Windows Hardening with Nmap

1. Return to the NMAP Console.
2. Run:
```
nmap -Pn <your IP>
```
3. Confirm:
  - Port 80 (HTTP) is closed
  - Port 53 (DNS) remains open

## Final Outcome

Linux Web Server: removed LDAP, secured SSH, enabled firewall allow-lists

Windows DNS Server: removed IIS, hardened admin, disabled unused accounts/services, enforced AppLocker

These steps reduce attack surface and improve baseline security posture.
