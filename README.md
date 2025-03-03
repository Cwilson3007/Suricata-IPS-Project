# 🚀 Suricata Intrusion Prevention System (IPS) Project

## 📝 Overview
# This project sets up Suricata as an IPS, creates custom rules, simulates attacks, and analyzes logs.

## 🎯 Objectives
# - Install and configure Suricata on Ubuntu.
# - Develop custom rules for network threat detection.
# - Simulate ICMP and SSH brute-force attacks.
# - Analyze Suricata logs for detection and prevention.

## 🛠️ Tools and Technologies
# - OS: Ubuntu 22.04
# - IDS/IPS: Suricata
# - Utilities: ping, ssh
# - Logs: fast.log, eve.json
# - Editors: Nano, Vim
# - Scripting: Bash

# =====================================================
# 🛠️ Installation and Setup
# =====================================================

# Step 1: Update and Install Suricata
sudo apt update && sudo apt install -y suricata

# Step 2: Verify Suricata Installation
suricata --build-info | grep "Suricata Version"

# Step 3: Enable and Check Suricata Service
sudo systemctl enable --now suricata
sudo systemctl status suricata

# Save screenshot: `screenshots/screenshot_01_install.png`

# Step 4: Configure Suricata for Network Traffic Capture
sudo nano /etc/suricata/suricata.yaml

# Ensure the following is set in `suricata.yaml`:
# AF_PACKET mode should be enabled:
# af-packet:
#   - interface: eth0
#     threads: auto
#     cluster-id: 99
#     cluster-type: cluster_flow

# Save and exit (Ctrl+X, then Y, then Enter).
# Save screenshot: `screenshots/screenshot_02_config.png`

# Step 5: Add Custom Detection Rules
cd /etc/suricata/rules
sudo nano custom.rules

# Add the following rules:

# ICMP Detection Rule
echo 'alert icmp any any -> any any (msg:"ICMP Traffic Detected"; sid:1000001; rev:1;)' | sudo tee -a custom.rules

# SSH Brute Force Detection Rule
echo 'alert tcp any any -> any 22 (msg:"Possible SSH Brute Force"; flow:to_server,established; threshold:type both, track by_src, count 5, seconds 10; sid:1000002; rev:1;)' | sudo tee -a custom.rules

# Save and exit.
# Save screenshot: `screenshots/screenshot_03_rules.png`

# Step 6: Include Custom Rules in Suricata Configuration
sudo nano /etc/suricata/suricata.yaml

# Add `custom.rules` under the `rule-files:` section:
# rule-files:
#   - /etc/suricata/rules/custom.rules

# Save and exit.
# Save screenshot: `screenshots/screenshot_04_suricata_config.png`

# Step 7: Test Suricata Configuration
sudo suricata -T -c /etc/suricata/suricata.yaml

# Save screenshot: `screenshots/screenshot_05_suricata_test.png`

# =====================================================
# 🛡️ Attack Simulation and Log Analysis
# =====================================================

# Step 8: Simulate ICMP (Ping) Attack
ping -c 5 8.8.8.8

# Check Suricata logs for ICMP alerts
sudo cat /var/log/suricata/fast.log | grep "ICMP Traffic Detected"

# Save screenshot: `screenshots/screenshot_06_icmp_alert.png`

# Step 9: Simulate SSH Brute Force Attack
for i in {1..6}; do ssh user@localhost; done

# Check Suricata logs for SSH brute force alerts
sudo cat /var/log/suricata/eve.json | jq '.alert'

# Save screenshot: `screenshots/screenshot_07_ssh_alert.png`

# =====================================================
# 📊 Findings and Conclusion
# =====================================================

# Suricata successfully detected ICMP and SSH brute-force attempts.
# Custom rules effectively triggered alerts.
# Log files (fast.log and eve.json) provided detailed evidence of the attacks.

# =====================================================
# 🚀 Next Steps
# =====================================================

# - Expand rule set to detect HTTP attacks, malware traffic.
# - Integrate Suricata with the ELK Stack for advanced log analysis.
# - Automate threat detection using Python.

# =====================================================
# 📂 Project Structure
# =====================================================

mkdir -p Suricata-IPS-Project/screenshots
cd Suricata-IPS-Project

touch README.md suricata_custom.rules suricata.yaml suricata_fast.log suricata_eve.json

tree Suricata-IPS-Project

# Expected Output:
# Suricata-IPS-Project/
# ├── README.md
# ├── suricata_custom.rules
# ├── suricata.yaml
# ├── suricata_fast.log
# ├── suricata_eve.json
# └── screenshots/
#     ├── screenshot_01_install.png
#     ├── screenshot_02_config.png
#     ├── screenshot_03_rules.png
#     ├── screenshot_04_suricata_config.png
#     ├── screenshot_05_suricata_test.png
#     ├── screenshot_06_icmp_alert.png
#     └── screenshot_07_ssh_alert.png

# =====================================================
# 👤 Author
# =====================================================

# Your Name
# Your Contact Information

# Ensure all referenced screenshots are placed in the `screenshots/` directory.
