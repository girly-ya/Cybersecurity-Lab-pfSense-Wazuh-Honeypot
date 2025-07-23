#  pfSense Setup Guide

This guide documents how I installed and configured **pfSense** as the core firewall/router in a virtual network lab using **VirtualBox**. The setup includes three network zones: **WAN**, **LAN**, and **DMZ** to simulate a real-world enterprise environment.

---

##  Video Installation Reference

The pfSense installation was based on a YouTube tutorial:  
🔗 [Installation Video](https://www.youtube.com/watch?v=Y-Dj8lHmXy8) 

---

##  VirtualBox Adapter Configuration

The pfSense VM was configured with **three network adapters**:

| Adapter | Type                     | VirtualBox Network Name | Purpose                         |
|---------|--------------------------|--------------------------|---------------------------------|
| 1       | **NAT**                  | —                        | Provides internet access (WAN) |
| 2       | **Internal Network**     | `lan`                    | Connects to Kali Linux (LAN)   |
| 3       | **Internal Network**     | `dmz`                    | Connects to Ubuntu Honeypot    |

>  Only Adapter 1 uses NAT. Adapters 2 and 3 use **Internal Networks** to simulate isolation between LAN and DMZ.

---

##  IP Addressing Scheme

| Zone | Interface on pfSense | pfSense IP         | Host OS (VM)         | Host IP            |
|------|----------------------|--------------------|----------------------|--------------------|
| WAN  | em0 (Adapter 1)      | Dynamic via NAT    | —                    | —                  |
| LAN  | em1 (Adapter 2)      | `192.168.1.1/24`   | Kali Linux           | `192.168.1.100`    |
| DMZ  | em2 (Adapter 3)      | `192.168.20.0/24`  | Ubuntu (Cowrie honeypot) | `192.168.20.11` |

---

##  Accessing pfSense Web Interface

From **LAN (Kali Linux)**, access the pfSense web interface at:
```
https://192.168.1.1
```
##  Adding and Configuring the DMZ Interface

1. Go to `Interfaces > Assignments` in the pfSense Web GUI.
2. You should see:
   - `WAN` (em0)
   - `LAN` (em1) → Rename to **LAN**, assign `192.168.1.1/24`
3. Add a new interface and assign it to the third adapter (DMZ).
 Click the new interface name (e.g., `OPT1`) to configure it:
   -  Enable the interface
   -  Rename to `DMZ`
   -  Assign static IP: `192.168.20.0/24`
   -  Save and apply changes.

---

##  Firewall Rules Setup

###  LAN Rules

- Allow **all traffic from LAN to any**:
  - This allows **Kali (192.168.1.100)** to access:
    - The internet (via NAT through pfSense).
    - The **DMZ zone** (Ubuntu Honeypot at `192.168.20.11`).
    - pfSense itself at `192.168.1.1`.

###  DMZ Rules

- Allow:
  - **ICMP (ping)** from DMZ to:
    - pfSense gateway `192.168.20.0`.
    - Kali (LAN) at `192.168.1.100`.
  - This ensures network-level communication for monitoring or testing.
