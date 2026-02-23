# In this section of Networking and Security we will learn how we can use Network Analysis through Collecting, Viewing, and Replaying Network Traffic using Packet Capuring.

## Part 1: Collect Packet Data with tcpdump


### 1.  Open the **Terminal** icon to open a bash prompt.

### 2. View Available Network Interfaces

```bash
tcpdump -D
```

* Identify the interface (e.g., `ens5`) used for network traffic.

### 3. Capture Packets

```bash
sudo tcpdump -i ens5 -s0 -w <your url>
```

This command:

* `-i ens5` → Captures traffic on interface **ens5**
* `-s0` → Captures full packet size
* `-w <your url>` → Saves output in pcap format

### 4. Generate Traffic

* Open a web browser.
* Visit:

```
http://<your IP address>
```

### 5. Stop Capture

* Return to the terminal.
* Press **Ctrl + C** to stop capturing.

### 6. View Captured Traffic

```bash
tcpdump -r <your url>
```
<img width="743" height="566" alt="Screenshot 2026-02-22 at 2 52 31 PM" src="https://github.com/user-attachments/assets/7081a9d2-2b81-4df3-a24e-ceae16c025e3" />

* Displays all captured traffic.

### 7️⃣ Filter Only Web Traffic

```bash
tcpdump -r <your url> host <your IP address> and port 80
```

* Filters only HTTP (port 80) traffic related to your IP.
<img width="939" height="533" alt="Screenshot 2026-02-22 at 2 54 11 PM" src="https://github.com/user-attachments/assets/b9d212ba-9fc0-4361-81b1-18e62097bb13" />

---

## Part 2: Capture Traffic Using Wireshark

Instead of command-line capture, you can visually analyze traffic using **Wireshark**.

### 1. Download Wireshark at https://www.wireshark.org/download.html

* Launch Wireshark

### 2. Select Interface

* Click on the **ens5** interface.

### 3. Apply Capture Filter

In the filter box, enter:

```
port 80
```

* Captures only HTTP traffic.

### 4. Start Capture

* Click the shark-fin **Start** button.
<img width="745" height="552" alt="Screenshot 2026-02-22 at 3 03 01 PM" src="https://github.com/user-attachments/assets/8824ea57-acb0-4897-b5bf-6326d9d904f5" />

### 5. Generate Traffic

* Open a browser and visit:

```
http://<your IP address>
```

### 6. Observe Packets

* Switch back to Wireshark.
* Each row represents a packet.
* Packet details appear in the lower pane.

---

## Part 3: Replay Traffic Using tcpreplay

To simulate previous traffic, use **tcpreplay**.

### 1. Stop Current Capture

* Click **Stop** in Wireshark.
* Close without saving.

### 2. Start New Capture

* Ensure filter is still:

```
port 80
```

* Click **Start**.

### 3. Replay Captured Traffic

Open a new terminal window and run:

```bash
sudo tcpreplay -i ens5 <your url>
```

* Replays all captured packets on interface ens5.

### 4️⃣ Observe in Wireshark
<img width="751" height="575" alt="Screenshot 2026-02-22 at 3 03 19 PM" src="https://github.com/user-attachments/assets/22c64835-b06e-4b67-8a86-8ebc7c1f8178" />

* You should see both request and response packets.

---

## Part 4: Replay Only Server Responses

### 1. Restart Capture

* Click **Restart current capture** in Wireshark.
* Continue without saving.

### 2. Replay Only Destination Traffic

Run:

```bash
sudo tcpdump -r <your url> -w- 'dst <your IP address>' | sudo tcpreplay -i ens5 -
```

This command:

* Reads packets from `<your url>`
* Filters packets destined for `<your IP address>`
* Pipes them to tcpreplay
* Replays only server response traffic

### 3. Verify

* Switch to Wireshark.
* Confirm only response packets appear.

---

# Summary

You learned how to:

1. Capture traffic with **tcpdump**
2. Filter relevant HTTP packets
3. Visually inspect traffic using **Wireshark**
4. Replay captured traffic using **tcpreplay**
5. Replay filtered response-only traffic

This workflow helps security teams collect, analyze, and simulate real network traffic for investigation and testing purposes.
