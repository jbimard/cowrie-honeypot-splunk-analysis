# Cowrie Honeypot + Splunk SIEM Analysis

![Project Status](https://img.shields.io/badge/status-complete-success)
![License](https://img.shields.io/badge/license-MIT-blue)

A comprehensive security research project deploying an SSH honeypot to capture real-world attack patterns and analyze attacker behavior using Splunk SIEM.

## 🎯 Project Overview

This project demonstrates end-to-end threat intelligence collection and analysis by:
- Deploying a Cowrie SSH honeypot to capture attack attempts
- Forwarding logs to Splunk for centralized analysis
- Building detection rules and dashboards for threat hunting
- Mapping attacker behavior to MITRE ATT&CK framework

**Key Metrics:**
- 422+ security events analyzed
- 40+ unique attack sessions captured
- 14 MITRE ATT&CK techniques identified
- 3 comprehensive Splunk dashboards created

## 🛠️ Technical Stack

| Component | Technology |
|-----------|-----------|
| Honeypot | Cowrie (SSH/Telnet) |
| SIEM | Splunk Enterprise |
| Log Forwarding | Splunk Universal Forwarder |
| Environment | Ubuntu 22.04 (ARM64) |
| Virtualization | UTM (Apple Silicon) |

## 📊 Dashboards

### 1. Attack Overview Dashboard
High-level metrics showing total events, login success/failure rates, top commands, and attack timeline.

![Dashboard 1](screenshots/dashboard-overview.png)

### 2. Threat Intelligence & Attack Chain Dashboard
Maps observed attacker behavior to MITRE ATT&CK techniques, visualizes kill chain progression, and scores threat severity.

![Dashboard 2](screenshots/dashboard-threat-intel.png)

### 3. Behavioral Analysis & Anomaly Detection Dashboard
Analyzes command sequences, credential targeting patterns, attacker persistence, and session characteristics.

![Dashboard 3](screenshots/dashboard-behavioral.png)

## 🔍 Key Findings

### Attack Patterns
- **Most targeted username:** root (95% of login attempts)
- **Average session duration:** 45 seconds (indicates automated attacks)
- **Common command sequence:** `whoami → uname -a → cat /etc/passwd`

### Threat Intelligence
- **Top MITRE ATT&CK technique:** T1087 - Account Discovery (40% of commands)
- **Malicious activity detected:** wget/curl download attempts, /etc/shadow access, privilege escalation
- **Attacker classification:** 60% one-time attackers, 30% repeat attackers, 10% persistent threats

### Security Recommendations
1. Disable root SSH login on production systems
2. Implement fail2ban with 3-attempt lockout threshold
3. Deploy network segmentation for critical assets
4. Enable multi-factor authentication for all remote access

[View detailed findings →](docs/03-analysis-findings.md)

## 📁 Repository Contents
```
├── docs/               # Detailed documentation
│   ├── setup-guide.md
│   ├── splunk-configuration.md
│   └── analysis-findings.md
├── queries/            # Splunk searches and detection rules
├── screenshots/        # Dashboard and analysis visuals
└── resume/            # Resume-ready project bullets
```

## 🚀 Quick Start

### Prerequisites
- Ubuntu 22.04 (or compatible Linux)
- 4GB+ RAM, 40GB+ disk space
- Splunk Enterprise (free tier) or Splunk Universal Forwarder

### Installation
```bash
# 1. Install Cowrie honeypot
git clone https://github.com/cowrie/cowrie
cd cowrie
python3 -m venv cowrie-env
source cowrie-env/bin/activate
pip install -r requirements.txt

# 2. Configure Cowrie
cp etc/cowrie.cfg.dist etc/cowrie.cfg
# Edit etc/cowrie.cfg - set SSH port, enable JSON logging

# 3. Start Cowrie
bin/cowrie start

# 4. Configure Splunk forwarder (see docs/splunk-configuration.md)
```

[Full setup guide →](docs/01-setup-guide.md)

## 📈 Splunk Queries

### Basic Analysis Queries
- [Event type breakdown](queries/basic-analysis.spl)
- [Login attempt analysis](queries/basic-analysis.spl)
- [Command execution timeline](queries/basic-analysis.spl)

### Detection Rules
- [Brute force detection](queries/detection-rules.spl)
- [Malicious command detection](queries/detection-rules.spl)
- [Credential theft indicators](queries/detection-rules.spl)

## 🎓 Skills Demonstrated

- **Threat Intelligence:** Collected and analyzed real-world attack data
- **SIEM Management:** Configured Splunk for security monitoring
- **Threat Hunting:** Created custom detection rules and correlation searches
- **Behavioral Analysis:** Identified attacker patterns and methodologies
- **Documentation:** Comprehensive technical writing and knowledge transfer

## 🔗 Related Links

- [Cowrie Honeypot Documentation](https://cowrie.readthedocs.io/)
- [Splunk Documentation](https://docs.splunk.com/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

## 📝 License

This project is licensed under the MIT License - see LICENSE file for details.

## 👤 Author

**Joseph Posas**
- Portfolio: [josephposas.com](https://josephposas.com)
- LinkedIn: [linkedin.com/in/josephposas](https://josephposas.com/linkedin)
- Email: joseph.posasm@gmail.com

---

*This project was created as part of cybersecurity skill development and threat intelligence research. All data collected is from simulated attack scenarios in an isolated lab environment.*
