# Wazuh Manager Setup (Quick Start)

This guide documents the steps I followed to install and configure the Wazuh Manager on Kali Linux using the official quickstart script (no Docker used).

---
## What is a Wazuh Manager?

The Wazuh Manager is the central server that receives, analyzes, and correlates security data sent by multiple Wazuh Agents. It provides:

- Collection and aggregation of logs and alerts  
- Real-time threat detection and analysis  
- Integration with dashboards for visualization  
- Management of agent registration and communication  
- Automated responses to security events
  
---

## System Requirements

Recommended specs based on number of agents:

| Number of Agents | CPU       | RAM    | Storage (90 days) |
|------------------|-----------|--------|-------------------|
| 1–25             | 4 vCPU    | 8 GiB  | 50 GB             |
| 25–50            | 8 vCPU    | 8 GiB  | 100 GB            |
| 50–100           | 8 vCPU    | 8 GiB  | 200 GB            |

In my setup, I used:
- **4 vCPU**
- **16 GB RAM**
- **Kali Linux (as host)**
- Wazuh Manager version: **4.12**

---

##  Installation Steps

1. Download and run the Wazuh installation script:

```bash
curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```
2. Wait for the installation to complete. At the end, you will see output like this:

    ```
    INFO: --- Summary ---
    INFO: You can access the web interface https://<WAZUH_DASHBOARD_IP_ADDRESS>
        User: admin
        Password: <ADMIN_PASSWORD>
    INFO: Installation finished.
    ```

---

## Accessing the Wazuh Dashboard

1. Open your browser and go to:

    ```
    https://<Your_Kali_IP>
    ```

2. Log in using:
    - **Username**: `admin`
    - **Password**: (displayed in the terminal after install)

> **Note:** Your browser may show a certificate warning. This is expected because it uses a self-signed certificate.  
> You can accept the warning and proceed, or replace it later with a certificate from a trusted authority.

---

##  Retrieving Stored Passwords

To get all the passwords generated during installation (for Wazuh Indexer, API, etc.), run:

```bash
sudo tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
