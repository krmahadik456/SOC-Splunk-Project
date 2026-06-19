# Incident Investigation Report — Frothly Inc. Breach

## 1. Executive Summary

Frothly Inc., a fictional beverage company, experienced a security incident on August 20, 2018. An attacker exploited a known vulnerability in Apache Struts2 (CVE-2018-11776) to compromise the company's web server.

The investigation revealed the attacker performed system reconnaissance, stole sensitive user information, created a persistent backdoor account, and established communication with an external command and control server.

**Severity:** Critical
**Incident Duration:** 29 minutes (16:35 – 17:04)

## 2. Incident Timeline

| Time | Action Taken | MITRE Category |
|------|--------------|----------------|
| 15:15 | Automated scanning of web applications | Reconnaissance |
| 16:35 | Remote code execution exploit launched | Initial Access |
| 16:36 | System discovery commands executed | Discovery |
| 16:36 | User account files accessed | Credential Access |
| 16:37 | Backdoor account created | Persistence |
| 16:37 | Operating system fingerprinting | Discovery |
| 16:38 | Kernel exploit uploaded | Privilege Escalation |
| 16:40 | Exploit decoded from base64 | Defense Evasion |
| 16:42 | Reverse shell mechanism prepared | C2 Preparation |
| 16:43 | External connection established | Command & Control |

## 3. Investigation Findings

### 3.1 Attacker Identification

The attacker was identified through analysis of web server logs. IP address `192.168.8.103` was observed sending multiple suspicious POST requests to the `/frothlyinventory/integration/saveGangster.action` endpoint.

### 3.2 Vulnerability Exploited

The attack exploited CVE-2018-11776, a remote code execution vulnerability in Apache Struts2. This vulnerability allows attackers to execute arbitrary system commands on the target server.

### 3.3 Attacker Actions

**Reconnaissance**
The attacker executed the following commands to understand the system:
- `whoami` — identify current user
- `id` — check user permissions
- `groups` — view group memberships
- `uname -a` — determine operating system version

**Credential Theft**
The attacker accessed the `/etc/passwd` file, which contains user account information for all system users.

**Persistence Creation**
A backdoor account named `tomcat7` was created using the `useradd` command, allowing the attacker to return at any time.

**Privilege Escalation Attempt**
The attacker uploaded a kernel exploit file named `colonel.c`. This file was encoded in base64 and later decoded, indicating an attempt to gain root privileges.

**Command & Control**
Two successful connections were established to external IP `45.77.53.176` on port `8088`, using netcat (`nc`) to create a reverse shell.

### 3.4 Indicators of Compromise (IoCs)

**Network Indicators**
- Attacker IP: 192.168.8.103
- C2 Server: 45.77.53.176
- C2 Port: 8088

**Host Indicators**
- Backdoor User: tomcat7
- Suspicious File: colonel.c
- Suspicious Path: /tmp/backpipe

## 4. MITRE ATT&CK Classification

| Technique | Description | ATT&CK ID |
|-----------|-------------|-----------|
| Exploit Public-Facing Application | Vulnerability exploitation | T1190 |
| Command and Scripting Interpreter | Command execution | T1059 |
| System Information Discovery | Reconnaissance | T1082 |
| OS Credential Dumping | Password file access | T1003 |
| Create Account | Backdoor creation | T1136 |
| Exploitation for Privilege Escalation | Kernel exploit attempt | T1068 |
| Application Layer Protocol | C2 communication | T1071 |

## 5. Remediation Recommendations

### Immediate Actions

1. **Block malicious IPs** — Configure firewall rules to block traffic to/from 45.77.53.176
2. **Remove backdoor account** — Delete the `tomcat7` user account
3. **Apply security patches** — Update Apache Struts2 to the latest version

### Short-Term Actions

4. **Deploy Web Application Firewall (WAF)** — Implement WAF rules to detect and block RCE attempts
5. **Monitor outbound traffic** — Set up alerting for suspicious outbound connections

### Long-Term Actions

6. **Implement network segmentation** — Isolate critical systems from less secure networks
7. **Conduct security training** — Educate developers on secure coding practices

## 6. Lessons Learned

This incident highlights several critical security gaps:

1. **Patch management** — The vulnerability was known and a patch was available. A proactive patching process would have prevented this attack.

2. **Network visibility** — Real-time monitoring could have detected the attack much earlier.

3. **Access controls** — The attacker was able to create a new user account. Stricter controls on account creation would have prevented this.

4. **Endpoint protection** — The kernel exploit attempt should have been detected by endpoint security tools.

## 7. Conclusion

The investigation successfully identified a complete attack chain against Frothly Inc. The attacker used a known vulnerability to gain initial access, performed reconnaissance, created a backdoor, attempted privilege escalation, and established command and control communication.

Immediate remediation steps have been prioritized to prevent similar incidents in the future.

---

**Prepared By:** [YOUR NAME]  
**Date:** [CURRENT DATE]  
**Classification:** Confidential
