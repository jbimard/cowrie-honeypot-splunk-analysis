# Cowrie Honeypot Setup Guide

## Overview
This guide explains how to deploy a Cowrie SSH honeypot for security research and threat intelligence collection.

## Prerequisites
- Ubuntu 22.04 (or compatible Linux distribution)
- 2GB+ RAM
- 20GB+ disk space
- Python 3.8+

## Installation Steps

### 1. Install Dependencies
```bash
sudo apt update
sudo apt install -y git python3-virtualenv libssl-dev libffi-dev build-essential python3-dev
```

### 2. Clone Cowrie Repository
```bash
cd ~
git clone https://github.com/cowrie/cowrie
cd cowrie
```

### 3. Create Virtual Environment
```bash
python3 -m venv cowrie-env
source cowrie-env/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Configure Cowrie
```bash
cp etc/cowrie.cfg.dist etc/cowrie.cfg
nano etc/cowrie.cfg
```

**Key configuration changes:**
- Set `hostname = svr04`
- Enable JSON logging: `[output_jsonlog]` section
- Configure SSH port (default 2222, or 22 if running as privileged)

### 5. Start Cowrie
```bash
bin/cowrie start
```

### 6. Verify Operation
```bash
# Check status
bin/cowrie status

# View logs
tail -f var/log/cowrie/cowrie.log
```

## Testing the Honeypot

From another machine:
```bash
ssh root@<honeypot-ip>
# Try password: password, admin, 123456
```

Commands will be logged to:
- Text: `var/log/cowrie/cowrie.log`
- JSON: `var/log/cowrie/cowrie.json`

## Security Considerations

⚠️ **Important:**
- Deploy in isolated network/VLAN
- Do NOT expose to internet without proper segmentation
- Monitor resource usage
- Regularly review collected data
- This is for RESEARCH purposes only

## Troubleshooting

**Cowrie won't start:**
- Check `var/log/cowrie/cowrie.log` for errors
- Verify Python virtual environment is activated
- Ensure port 2222 (or configured port) is not in use

**No logs being generated:**
- Check file permissions in `var/log/cowrie/`
- Verify JSON logging is enabled in `cowrie.cfg`

## Next Steps

After Cowrie is running:
1. Configure Splunk Universal Forwarder (see `02-splunk-configuration.md`)
2. Generate test attack traffic
3. Begin analysis in Splunk

## Resources
- [Official Cowrie Documentation](https://cowrie.readthedocs.io/)
- [Cowrie GitHub Repository](https://github.com/cowrie/cowrie)
