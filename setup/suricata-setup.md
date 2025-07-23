#  Suricata Setup (IDS)

This guide details how to install and configure **Suricata** on Ubuntu for use as an Intrusion Detection System (IDS) in your cybersecurity lab. Suricata will analyze traffic and detect suspicious patterns based on signatures.
#  What is Suricata?

**Suricata** is a powerful, open-source **Network Intrusion Detection System (NIDS)**, **Intrusion Prevention System (IPS)**, and **Network Security Monitoring (NSM)** engine.  
It inspects network traffic in real-time, detects suspicious activities using both **signature-based** and **anomaly-based** techniques, and can generate alerts or block malicious traffic.

### Key Features:
-  **Deep Packet Inspection (DPI)**
-  **Multi-threading for high performance**
-  **EVE JSON logging output**
-  **Supports integration with SIEM platforms like Wazuh**
---


## 1. Installation

Run the following commands:

```bash
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt update
sudo apt install suricata jq
```
## 2.Check Suricata's build and status:

```bash
sudo suricata --build-info
sudo systemctl status suricata
```
## 2. Basic Configuration
### 1. Identify Network Interface
Use the following command to identify the interface Suricata will monitor:

```bash
ip addr
```
Example output:
```
2: enp1s0: <...>
    inet 10.0.0.23/24 ...
Here, enp1s0 is the interface and 10.0.0.23 is your local IP.
```
### 2. Edit Configuration File
Edit Suricata's main configuration file:

```bash
sudo nano /etc/suricata/suricata.yaml
```
Key Settings to Review:
HOME_NET: The default includes common private network ranges like 10.0.0.0/8, 192.168.0.0/16, and 172.16.0.0/12. Make sure your LAN IP is included.
af-packet section: Match it with your detected interface.

Example config:
```
af-packet:
  - interface: enp1s0
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
    tpacket-v3: yes
```

### 3. Signatures (Rule Sets)
Suricata uses signatures/rules to detect intrusions.
Use the suricata-update tool to fetch and install the ET Open ruleset:

```bash
sudo suricata-update
```
Rules will be saved to:
/var/lib/suricata/rules/suricata.rules
### 4. Running Suricata
Once rules are installed and configuration is done, restart Suricata:

```bash
sudo systemctl restart suricata
```
### 5. Verify Logs
To verify Suricata is working correctly, check its logs:

```bash
sudo tail -f /var/log/suricata/suricata.log
```
Rule trigger:
```bash
sudo tail -f /var/log/suricata/fast.log
```
Alerts:
```bash
sudo tail -f /var/log/suricata/eve.json | jq
```
## 3. Forward Suricata Logs to Wazuh
To make Wazuh read Suricata alerts, we monitor the EVE JSON log file.

**Configure Wazuh Agent**
Edit the Wazuh Agent config file:

```bash
sudo nano /var/ossec/etc/ossec.conf
```
Inside the <ossec_config> tag, add:
```
<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
</localfile>
```
## 4. Customize Wazuh Rules for Suricata Alerts (Optional)

By default, Wazuh detects and classifies Suricata alerts from `/var/log/suricata/eve.json` using its built-in rules. These alerts are shown in the dashboard with groups like:

- **Group**: `suricata`
- **ID**: e.g. `88003`
- **Description**: `ET POLICY curl User-Agent Outbound`

However, you can also create custom Wazuh rules to fine-tune the behavior or add extra labels for lab testing.

---

###  Example: Add Custom Rule for Suricata Event

1. Open Wazuh's **custom rules file**:

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
```
Add a custom rule that matches a Suricata alert using its event_type, alert.signature, or any other field from the JSON log.
Example: Match Suricata alert with signature "ICMP ping detected":
```
<group name="suricata,ids,icmp">
  <rule id="100101" level="5">
    <decoded_as>json</decoded_as>
    <field name="event_type">alert</field>
    <field name="alert.signature">ICMP ping detected</field>
    <description>Custom ICMP ping alert detected by Suricata</description>
  </rule>
</group>
```
Save and restart the Wazuh agent:
```bash
sudo systemctl restart wazuh-agent
```
