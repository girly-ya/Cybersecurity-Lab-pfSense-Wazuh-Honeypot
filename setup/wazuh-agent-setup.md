#  Wazuh Agent Setup (Linux)

This guide explains how to deploy the Wazuh Agent on a Linux endpoint (e.g., Ubuntu honeypot). The agent collects logs, monitors the system, and sends data securely to the Wazuh Manager.

---

## What is a Wazuh Agent?

The Wazuh Agent runs on the host you want to monitor. It communicates with the Wazuh Manager via an **encrypted and authenticated** channel, sending:

- System logs  
- Security alerts  
- File integrity data  
- And more...

---

## Add the Wazuh Repository

1. **Install the Wazuh GPG key**:

   ```bash
   curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | \
   gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && \
   chmod 644 /usr/share/keyrings/wazuh.gpg
   ```

2. **Add the Wazuh repository**:

    ```bash
    echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | \
    sudo tee -a /etc/apt/sources.list.d/wazuh.list
    apt-get update
    ```

---

## Deploy a Wazuh agent

1. **Install and Register the Agent**:

    ```bash
    WAZUH_MANAGER="YOUR_WAZUH_MANAGER_IP" sudo apt-get install wazuh-agent
    ```

2. **Enable and start the Wazuh agent service**.
   ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable wazuh-agent
    sudo systemctl start wazuh-agent
   ```
3. **Verification**:
   ```bash
   sudo systemctl status wazuh-agent
   ```
