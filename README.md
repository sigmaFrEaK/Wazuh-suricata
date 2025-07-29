# 🛡️ Home SOC Lab: Suricata IDS + Wazuh SIEM Integration

This project demonstrates how to set up a functional **Network Intrusion Detection System (NIDS)** using **Suricata 6.0.8** integrated with **Wazuh 4.7**, providing real-time alerting, log collection, and threat visibility.

---

## 🔧 Technologies Used

- **Suricata 6.0.8** – NIDS/IPS engine
- **Wazuh 4.7** – Security Information and Event Management (SIEM)
- **Ubuntu** – Host OS
- **Kali Linux** – Attacker simulation
- **Custom scripts** – Automation for dependencies and rules

---

## ⚙️ Setup Instructions

### 1. 📦 Install Dependencies

```bash
sudo bash dependancies.sh
```

This script installs all required packages before compiling Suricata.

---

### 2. 🐍 Download & Compile Suricata 6.0.8

```bash
wget https://www.openinfosecfoundation.org/download/suricata-6.0.8.tar.gz
tar xzvf suricata-6.0.8.tar.gz
cd suricata-6.0.8

./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var \
--enable-nfqueue --enable-geoip --enable-lua --enable-hiredis

make
sudo make install
sudo make install-conf
sudo make install-rules
```

---

### 3. 👤 Create Suricata User & Directories

```bash
sudo useradd -r -s /sbin/nologin suricata
sudo mkdir -p /var/log/suricata
sudo chown -R suricata:suricata /var/log/suricata/
```

Make sure to assign proper file permissions to directories you create or edit.

---

### 4. ⚖️ Download Suricata Rules

```bash
sudo bash rules.sh
```

This script automates the rule download process.

---

### 5. 🧩 Configure Suricata Service

Create a systemd service file:

```bash
sudo nano /etc/systemd/system/suricata.service
```

Paste the following:

```ini
[Unit]
Description=Suricata IDS/IPS/NSM Engine
After=network.target

[Service]
User=suricata
Group=suricata
ExecStart=/usr/bin/suricata -c /etc/suricata/suricata.yaml -i eth0
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

> 🔁 Replace `eth0` with your actual network interface (e.g., `enp0s3`)

---

### 6. 📄 Configuration File

Copy `suricata.yaml` to:

```bash
/etc/suricata/suricata.yaml
```

Update the following:

- `HOME_NET` to match your subnet (e.g., `192.168.1.0/24`)
- Interface field (`eth0` or whatever yours is)

---

### 7. 🚀 Start Suricata

```bash
sudo systemctl daemon-reload
sudo systemctl enable suricata
sudo systemctl start suricata
```

You can check status with:

```bash
sudo systemctl status suricata
```

---

## 🔗 Wazuh Agent Integration

To connect Suricata logs with the Wazuh agent:

1. Copy your `wazuh-agent-configs` into:

```bash
/var/ossec/etc/ossec.conf
```

2. Restart the agent:

```bash
sudo systemctl restart wazuh-agent
```

> ⚠️ Add decoding rules and log paths if needed for Suricata log parsing (e.g., `/var/log/suricata/eve.json`)

---

## ✅ What's Next

- [ ] Simulate common attacks from Kali (SSH brute force, reverse shell)
- [ ] Detect with Suricata and alert in Wazuh
- [ ] Build dashboards or alerts based on MITRE ATT&CK techniques
- [ ] Add Sigma rules or integrate TheHive for incident handling

---

## 🧠 Author

**Deaa Alomari**  
Cybersecurity Graduate | SOC Analyst in Progress  
📫 [LinkedIn](https://www.linkedin.com/in/deaa-alomari-5680171aa?utm_source=share&utm_campaign=share_via&utm_content=profile&utm_medium=ios_app)

---

## 💬 Want feedback or want to collaborate?

Feel free to open an issue or connect with me!










