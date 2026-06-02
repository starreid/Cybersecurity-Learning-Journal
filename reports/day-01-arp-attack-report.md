## Day 1: ARP-Spoof

**Environment:**

- Attacker: Kali Linux
- Target: Metasploitable (IP `192.168.1.138`)
- Gateway: `192.168.1.1`

---

## 1. Network Traffic Capture (Data Collection)

**Command:**

```bash
sudo tshark -i eth0 -w arp-spoof.pcapng
```

**What it does:**
Starts capturing all packets passing through the `eth0` interface and saves them to the file `arp-spoof.pcapng`. This allows for later forensic analysis of the traffic.

---

## 2. Initial Analysis of ARP Packets

**Command:**

```bash
sudo tshark -r /tmp/arp-spoof.pcapng -Y "arp" | head -10
```

**What it does:**
Reads the `.pcapng` file generated in the previous step, filters only **ARP** protocol packets, and displays the first 10 lines. This step confirms that the network is exchanging ARP messages (Requests, Replies, Gratuitous ARP) between devices.

---

## 3. Connectivity Verification

**Command:**

```bash
ping -c 4 192.168.1.178
```

**What it does:**
Sends 4 ICMP (Ping) packets to IP `192.168.1.178` (the Kali machine's IP). This confirms that the target (Metasploitable) can communicate with the attacker before the attack begins.

