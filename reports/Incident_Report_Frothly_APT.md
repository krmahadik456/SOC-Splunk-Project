# Incident Response Report: Frothly Inc. APT Attack

## 1. Executive Summary

On August 20, 2018, Frothly Inc., a fictional brewing company, was targeted by an Advanced Persistent Threat (APT) attack. The attacker exploited a critical vulnerability in Apache Struts2 (CVE-2018-11776) to gain initial access to the web server [reference:6].

The investigation revealed that the attacker performed reconnaissance, stole sensitive system information, created a backdoor account for persistence, attempted privilege escalation using a kernel exploit, and established command and control (C2) communication with an external server.

**Severity: CRITICAL**
**Attack Duration: 29 minutes (16:35 — 17:04)**

## 2. Attack Timeline

| Time | Activity | MITRE Tactic |
|------|----------|--------------|
| 15:15 | Automated scanning of SuiteCRM (every 1 minute) | Reconnaissance |
| 16:35 | Apache Struts2 RCE exploit begins | Initial Access (T1190) |
| 16:36 | Reconnaissance (whoami, id, groups) | Discovery (T1082) |
| 16:36 | Credential theft (cat /etc/passwd) | Credential Access (T1003) |
| 16:37 | Backdoor account created (useradd tomcat7) | Persistence (T1136) |
| 16:37 | OS fingerprinting (uname -a, lsb_release) | Discovery |
| 16:38 | Kernel exploit uploaded (colonel.c via base64) | Privilege Escalation (T1068) |
| 16:40 | Exploit decoded (base64 --decode) | Defense Evasion (T1027) |
| 16:42 | Reverse shell pipe created (mknod /tmp/backpipe) | Command & Control |
| 16:43 | C2 connection established (nc 45.77.53.176:8088) | Command & Control (T1071) |
| 17:04 | Second C2 connection attempt | Command & Control |

## 3. Key Findings

### 3.1 Attacker Identification
- **Attacker IP:** 192.168.8.103 (Internal network)
- **Method of Detection:** Repeated suspicious POST requests to `/frothlyinventory/integration/saveGangster.action`

### 3.2 Vulnerability Exploited
- **Vulnerability:** Apache Struts2 Remote Code Execution
- **CVE ID:** CVE-2018-11776
- **Impact:** Remote code execution allowing attacker to run arbitrary commands on the server

### 3.3 Persistence Mechanism
- **Backdoor Account:** `tomcat7` was created using the `useradd` command
- **Risk:** Attacker can re-enter the system at any time using this account

### 3.4 Privilege Escalation Attempt
- **Exploit Used:** `colonel.c` — a known Linux kernel exploit
- **Status:** Exploit was uploaded and decoded but execution not confirmed

### 3.5 Command & Control
- **C2 Server:** 45.77.53.176:8088
- **Method:** Reverse shell using netcat (`nc`)
- **Attempts:** 2 successful C2 connections established

### 3.6 Commands Executed

Total 14 unique commands were executed by the attacker:

| Command | Purpose |
|---------|---------|
| whoami | User identification |
| id | User permissions check |
| groups | Group membership check |
| cat /etc/passwd | Steal user account information |
| uname -a | OS fingerprinting |
| lsb_release | Distribution information |
| useradd tomcat7 | Create backdoor account |
| base64 -d colonel.b64 | Decode kernel exploit |
| mknod /tmp/backpipe | Create reverse shell pipe |
| nc 45.77.53.176:8088 | Establish C2 connection |

## 4. MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|--------|-----------|-----|
| Initial Access | Exploit Public-Facing Application | T1190 |
| Execution | Command and Scripting Interpreter | T1059 |
| Discovery | System Information Discovery | T1082 |
| Credential Access | OS Credential Dumping | T1003 |
| Persistence | Create Account | T1136 |
| Privilege Escalation | Exploitation for Privilege Escalation | T1068 |
| Defense Evasion | Obfuscated Files or Information | T1027 |
| Command & Control | Application Layer Protocol | T1071 |
| Lateral Movement | Ingress Tool Transfer | T1105 |

## 5. Indicators of Compromise (IoCs)

### Network IoCs
| Type | Value |
|------|-------|
| Attacker IP | 192.168.8.103 |
| C2 Server IP | 45.77.53.176 |
| C2 Port | 8088 |

### Host IoCs
| Type | Value |
|------|-------|
| Backdoor User | tomcat7 |
| Exploit File | colonel.c |
| Suspicious Path | /tmp/backpipe |

### URL IoCs
| Type | Value |
|------|-------|
| Exploit Endpoint | /frothlyinventory/integration/saveGangster.action |

## 6. Recommendations

### Immediate Actions (24 Hours)
1. ✅ Block outbound traffic to **45.77.53.176:8088** at the firewall
2. ✅ Disable/delete the backdoor account **tomcat7**
3. ✅ Patch Apache Struts2 to the latest version (CVE-2018-11776)

### Short-term Actions (1 Week)
4. Implement Web Application Firewall (WAF) rules to detect RCE attempts
5. Enable comprehensive monitoring of all outbound connections
6. Review and rotate all system credentials

### Long-term Actions (1 Month)
7. Deploy IDS/IPS Snort rules for exploit detection
8. Implement application whitelisting
9. Conduct security awareness training for developers

## 7. Lessons Learned

1. **Vulnerability Management:** The Apache Struts2 vulnerability was known and a patch was available. Proactive patching would have prevented this attack.

2. **Network Segmentation:** The attacker was able to move laterally within the internal network. Network segmentation would have contained the breach.

3. **Monitoring Gaps:** The attack was only discovered through retrospective log analysis. Real-time monitoring and alerting could have detected this earlier.

4. **Endpoint Protection:** The kernel exploit attempt (`colonel.c`) should have been detected by endpoint protection tools.

## 8. Conclusion

This investigation successfully identified the full attack chain of an APT targeting Frothly Inc. The attacker exploited CVE-2018-11776 to gain initial access, performed reconnaissance, created a backdoor, attempted privilege escalation, and established C2 communication. All findings have been mapped to the MITRE ATT&CK framework.

Immediate remediation actions have been identified and prioritized to prevent similar attacks in the future.

---

**Report Prepared By:** [YOUR NAME]
**Date:** [CURRENT DATE]
**Classification:** CONFIDENTIAL
