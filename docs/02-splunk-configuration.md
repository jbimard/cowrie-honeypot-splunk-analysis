# Splunk Configuration Guide

## Architecture Overview
```
┌─────────────────┐         ┌──────────────────┐
│ Ubuntu VM       │         │ Splunk Server    │
│                 │         │ (Mac/Server)     │
│ Cowrie Honeypot │──logs──▶│                  │
│ + Forwarder     │ TCP:9997│ Splunk Enterprise│
└─────────────────┘         └──────────────────┘
```

## Part 1: Configure Splunk Enterprise (Receiving End)

### 1. Enable Data Receiving

**In Splunk Web UI:**
1. Settings → Forwarding and receiving
2. Click "Configure receiving"
3. Click "New Receiving Port"
4. Port: `9997`
5. Click "Save"

### 2. Create Honeypot Index

1. Settings → Indexes
2. Click "New Index"
3. Index Name: `honeypot`
4. Click "Save"

## Part 2: Install Splunk Universal Forwarder (Honeypot VM)

### 1. Download Forwarder
```bash
cd ~
wget -O splunkforwarder.tgz \
  "https://download.splunk.com/products/universalforwarder/releases/[VERSION]/linux/splunkforwarder-[VERSION]-linux-arm64.tgz"
```

### 2. Extract and Install
```bash
sudo tar xvzf splunkforwarder.tgz -C /opt
```

### 3. Start Forwarder (First Time)
```bash
sudo /opt/splunkforwarder/bin/splunk start --accept-license

# Create admin credentials when prompted
```

### 4. Configure Forward Server
```bash
# Replace <SPLUNK_SERVER_IP> with your Splunk server's IP
sudo /opt/splunkforwarder/bin/splunk add forward-server <SPLUNK_SERVER_IP>:9997
```

### 5. Add Cowrie Logs to Forwarder
```bash
sudo /opt/splunkforwarder/bin/splunk add monitor \
  /home/cowrie/cowrie/var/log/cowrie/ \
  -index honeypot \
  -sourcetype _json
```

### 6. Restart Forwarder
```bash
sudo /opt/splunkforwarder/bin/splunk restart
```

## Part 3: Verify Data Flow

### On Forwarder (VM):
```bash
# Check forwarder status
sudo /opt/splunkforwarder/bin/splunk status

# View what's being monitored
sudo /opt/splunkforwarder/bin/splunk list monitor

# View forward server
sudo /opt/splunkforwarder/bin/splunk list forward-server
```

### On Splunk Server:

**Search:**
```spl
index=honeypot
```

You should see Cowrie events within 1-2 minutes.

## Troubleshooting

### No Data Appearing in Splunk

**Check 1: Network connectivity**
```bash
# From VM, test connection to Splunk server
telnet <SPLUNK_SERVER_IP> 9997
```

**Check 2: Forwarder logs**
```bash
sudo tail -100 /opt/splunkforwarder/var/log/splunkd.log | grep -i error
```

**Check 3: File permissions**
```bash
ls -la /home/cowrie/cowrie/var/log/cowrie/
# Files should be readable (r--)
```

**Check 4: Firewall**
- Ensure port 9997 is allowed on Splunk server
- Check both host firewall and network firewalls

### Forwarder Connection Issues

**View connection status:**
```bash
sudo /opt/splunkforwarder/bin/splunk list forward-server -auth admin:<password>
```

**Should show:**
```
Active forwards:
    <SPLUNK_SERVER_IP>:9997
```

## Performance Tuning

**For resource-constrained VMs:**

Edit `/opt/splunkforwarder/etc/system/local/limits.conf`:
```ini
[thruput]
maxKBps = 256
```

## Security Best Practices

1. **Use TLS for forwarder connections** (production)
2. **Restrict forwarder to specific index**
3. **Monitor forwarder performance**
4. **Regularly rotate logs**

## Next Steps

Once data is flowing:
1. Create saved searches (see `queries/` folder)
2. Build dashboards
3. Set up alerts for critical events

## Resources
- [Splunk Universal Forwarder Documentation](https://docs.splunk.com/Documentation/Forwarder)
- [Getting Data In Guide](https://docs.splunk.com/Documentation/Splunk/latest/Data/WhatSplunkcanmonitor)
