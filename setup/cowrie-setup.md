# Cowrie Honeypot Setup (Ubuntu)

This guide covers how to install, configure, and run Cowrie, a medium interaction SSH honeypot designed to log attacker activity and gather threat intelligence.

---

## Prerequisites

- Ubuntu 18.04/20.04 or similar Linux server (your DMZ machine)  
- Python 3.6+ installed  
- Basic Linux command-line familiarity  
- Root or sudo privileges  

---

##  Installation Steps

### 1. Update and install dependencies

```bash
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install -y git python3 python3-pip python3-venv libssl-dev libffi-dev build-essential libpython3-dev authbind
```
### 2. Create a dedicated user for Cowrie
```bash
sudo adduser --disabled-password --gecos "" cowrie
```
### 3. Clone the Cowrie repository
```bash
sudo -u cowrie git clone https://github.com/cowrie/cowrie.git /home/cowrie/cowrie
```
### 4. Setup Python virtual environment and install Python dependencies
```bash
cd /home/cowrie/cowrie
sudo -u cowrie python3 -m venv cowrie-env
sudo -u cowrie ./cowrie-env/bin/pip install --upgrade pip
sudo -u cowrie ./cowrie-env/bin/pip install -r requirements.txt
```
### 5. Configure Cowrie
Copy the sample configuration to create your config:

```bash
sudo -u cowrie cp etc/cowrie.cfg.dist etc/cowrie.cfg
```
Edit /home/cowrie/cowrie/etc/cowrie.cfg to adjust settings such as:

Listening port (default 2222, change if needed)

Log file paths

Enabling or disabling certain features (like SSH banner, fake filesystem)

Adjusting output formats (JSON logging for Wazuh compatibility)
###  6. Start Cowrie
```bash
sudo -u cowrie ./bin/cowrie start
```
Check running processes with:

```bash
ps aux | grep cowrie
```
---

## Forwarding Logs to Wazuh

Once Cowrie is up and running, you can forward its logs to Wazuh for centralized monitoring and correlation.

---

###  Step 1: Locate Cowrie’s JSON Log File

Cowrie stores its logs (by default) in:
/home/cowrie/cowrie/var/log/cowrie/cowrie.json
This file contains structured JSON log entries of all attacker sessions.

---

### Step 2: Configure Wazuh Agent to Monitor the Log

1. Open the Wazuh Agent local configuration file:

```bash
sudo nano /var/ossec/etc/ossec.conf
```
Inside the <ossec_config> section, locate the <localfile> tags or insert a new one:
```
<localfile>
  <log_format>json</log_format>
  <location>/home/cowrie/cowrie/var/log/cowrie/cowrie.json</location>
</localfile>
```
**This tells the Wazuh agent to watch Cowrie’s JSON log file and forward events to the Wazuh manager**.

### Step 3: Restart the Wazuh Agent
Apply the changes by restarting the agent:

```bash
sudo systemctl restart wazuh-agent
sudo systemctl status wazuh-agent
```
