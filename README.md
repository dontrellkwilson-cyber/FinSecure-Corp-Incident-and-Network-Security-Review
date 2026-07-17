# FinSecure Corp. Incident Response and Network Security Review

## Project Overview

This project presents an incident response and network security assessment for **FinSecure Corp.**, a fictional financial organization. The review evaluates how the company responded to a malware infection and examines weaknesses in its network architecture, firewall rules, intrusion detection controls, internal segmentation, and security monitoring.

The assessment focuses on the **GhostX remote access Trojan incident**, the effectiveness of FinSecure's response actions, weaknesses in the existing network design, and recommended firewall and IDS improvements.

---

## Project Objectives

- Evaluate FinSecure's incident response actions.
- Identify strengths and weaknesses in detection, containment, eradication, and recovery.
- Recommend improvements to incident response procedures.
- Identify vulnerabilities in the network architecture.
- Evaluate weaknesses in firewall and IDS rules.
- Recommend network segmentation and access-control improvements.
- Explain how the proposed changes support confidentiality, integrity, and availability.
- Develop improved firewall and IDS rules based on least privilege and default-deny principles.

---

## Skills Demonstrated

- Incident Response Analysis
- Malware Incident Evaluation
- NIST Incident Response Concepts
- Network Architecture Review
- Firewall Rule Analysis
- Intrusion Detection and Prevention
- Network Segmentation
- DMZ Design
- Controlled Egress Filtering
- Lateral Movement Prevention
- Security Monitoring
- Threat Containment
- CIA Triad Analysis
- Least-Privilege Network Access

---

## Incident Summary

On June 24, 2025, an employee in the Human Resources department opened a malicious PDF attachment from a phishing email. The attachment silently installed the **GhostX remote access Trojan** on the employee's workstation.

The centralized intrusion detection system later detected unusual outbound traffic. FinSecure isolated the workstation, identified the malware, restored the endpoint from backup, scanned the HR subnet, and updated antivirus definitions across the organization.

---

## Incident Timeline

| Time | Event |
|---|---|
| June 24, 2025 – 9:12 a.m. | HR employee received a phishing email containing an “Updated Benefits Plan” PDF |
| June 24, 2025 – 9:14 a.m. | The attachment was opened and malware was installed |
| June 24, 2025 – 9:50–9:52 a.m. | The IDS detected possible GhostX command-and-control traffic |
| June 24, 2025 – 10:07 a.m. | The affected workstation was isolated from the network |
| June 24, 2025 – 11:25 a.m. | The malware was identified as GhostX RAT |
| June 24, 2025 – 1:45 p.m. | The endpoint was restored from backup and antivirus scans began on the HR subnet |
| June 25, 2025 – 8:30 a.m. | Antivirus definitions were updated across all endpoints |
| June 25, 2025 – 10:00 a.m. | An internal review was started |

---

## Incident Response Evaluation

### Action 1: IDS Detection and Notification

The centralized IDS detected possible command-and-control traffic from the infected workstation and notified on-call IT staff.

**Strengths:**

- The IDS identified suspicious outbound behavior.
- Automated notifications helped alert the response team.
- The alert started the containment and investigation process.

**Weaknesses:**

- The malicious attachment was opened at 9:14 a.m.
- The suspicious activity was not detected until approximately 9:50–9:52 a.m.
- The 36–38-minute delay gave the RAT time to communicate externally or attempt additional activity.

**Assessment:**

The IDS provided valuable detection, but endpoint-level monitoring could have identified the infection sooner.

---

### Action 2: Workstation Isolation

FinSecure disabled the affected workstation's network connection approximately 15 minutes after the IDS alert.

**Strengths:**

- Isolation reduced the chance of additional command-and-control communication.
- Disconnecting the workstation limited the possibility of further network access.
- The response team acted quickly after receiving the alert.

**Weaknesses:**

- A later IDS alert showed SMB traffic from the compromised HR workstation to a Finance system.
- The response documentation did not clearly confirm whether isolation was fully successful.
- The team needed to verify that the switch port was disabled and that the endpoint could not communicate through another path.

**Assessment:**

The containment action was appropriate, but FinSecure needed stronger verification that the endpoint was completely isolated.

---

### Action 3: Malware Identification and Recovery

FinSecure identified the malware as GhostX RAT, restored the endpoint from backup, and initiated antivirus scans.

**Strengths:**

- The organization identified the malware family.
- The affected endpoint was restored.
- The HR subnet was scanned for additional infections.
- Antivirus definitions were later updated throughout the organization.

**Weaknesses:**

- The documentation did not confirm that the backup was validated before restoration.
- The endpoint's persistence mechanisms were not clearly checked.
- Antivirus definitions were not updated until the following morning.
- The process did not clearly show that the restored endpoint was monitored before returning to service.

**Assessment:**

The recovery actions were useful, but additional validation was needed before declaring the system safe.

---

## Incident Response Improvements

### Improvement 1: Faster Detection and Automatic Containment

FinSecure should automatically quarantine endpoints when a high-confidence command-and-control alert is detected.

The process should:

- Isolate the device through endpoint detection and response or network access controls.
- Confirm that the device cannot communicate internally or externally.
- Alert the security team immediately.
- Preserve logs and evidence before remediation.
- Require analyst approval before reconnecting the system.

This would reduce the amount of time malware has to spread, communicate with external systems, or access sensitive data.

---

### Improvement 2: Required Post-Incident Review

FinSecure should conduct a post-incident review after every significant malware incident, even when only one department is affected.

The review should verify:

- Whether the backup was clean and trustworthy.
- Whether antivirus and detection signatures were current.
- Whether persistence mechanisms remained.
- Whether user credentials were exposed.
- Whether other systems were affected.
- Whether firewall, DNS, or proxy logs showed additional activity.
- Whether the restored system remained under monitoring.

A documented review would help FinSecure identify failures and prevent the same weaknesses from appearing in future incidents.

---

## Network Architecture Findings

### Vulnerability 1: Public Servers Are Not Separated Into a DMZ

The web server and mail server are placed behind the same firewall as the internal network without a separate demilitarized zone.

**Risk:**

If an internet-facing server is compromised, an attacker may gain a path toward internal HR, Finance, or administrative systems.

**Recommendation:**

Create a screened DMZ for the public web and mail servers.

- Allow HTTPS to the web server.
- Allow SMTP to the mail server.
- Deny DMZ-to-internal traffic by default.
- Allow only documented business-required connections.
- Log and monitor all permitted DMZ-to-internal traffic.

---

### Vulnerability 2: HR and Finance Lack Strong Internal Segmentation

The HR and Finance departments use separate subnets, but the existing firewall rules allow overly broad access between internal networks.

**Risk:**

- Malware can move laterally between departments.
- HR systems may reach Finance systems over SMB.
- Sensitive payroll or financial data may be exposed.
- A compromise in one subnet can affect another subnet.

**Recommendation:**

- Place HR and Finance in separate VLANs.
- Route inter-VLAN traffic through a firewall or Layer 3 access-control policy.
- Deny HR-to-Finance traffic by default.
- Allow only specific business-required services.
- Block unnecessary SMB traffic between departments.
- Log denied east-west traffic.

---

### Vulnerability 3: Firewall Administration Is Too Broadly Exposed

The firewall rule set allows any source to access the firewall management console on port 8080.

**Risk:**

An attacker may attempt to guess credentials, exploit the management service, modify firewall rules, or disable protections.

**Recommendation:**

- Restrict firewall administration to a dedicated management network.
- Allow access only from approved administrator IP addresses.
- Require VPN access for remote administration.
- Require multifactor authentication.
- Disable public access to the management interface.
- Log all administrative activity.

---

## CIA Triad Impact

### Confidentiality

The proposed changes reduce unauthorized access to financial, payroll, HR, and administrative data by limiting unnecessary network paths and restricting management access.

### Integrity

Stronger segmentation and firewall administration controls make it harder for attackers to alter systems, firewall rules, files, or internal services.

### Availability

A DMZ, controlled internal traffic, and protected management interfaces reduce the likelihood that a compromised system can disrupt critical network services.

---

## Firewall Rule Weaknesses

### Rule 102: Any Source to Any Internal Service

The existing rule allows any source to reach internal networks using any service.

**Security Exposure:**

- External scanning
- Unauthorized inbound connections
- Direct access attempts against internal systems
- Lateral movement between internal departments
- Increased attack surface

---

### Rule 103: Unrestricted Internal-to-External Traffic

The existing rule allows internal systems to connect to any external destination over any port or service.

**Security Exposure:**

- Malware command-and-control traffic
- Data exfiltration
- Direct use of external DNS
- Communication with malicious domains
- Unapproved outbound connections

---

## Relevant IDS Alerts

| Alert | Source | Destination | Security Concern |
|---|---|---|---|
| Outbound command-and-control traffic | `10.1.1.45` | `45.33.122.88` | Possible GhostX RAT communication |
| Port scan attempt | External host | `10.1.2.4` | Reconnaissance against an internal Finance system |
| Unauthorized SMB access | `10.1.1.45` | `10.1.2.10` | Possible lateral movement from HR to Finance |
| Suspicious DNS query | `10.1.1.45` | External DNS | Query to a known malicious domain |

---

## Proposed Firewall and IDS Rules

### Proposed Rule 1: Replace Broad Internal Access

| Source | Destination | Service | Action | Purpose |
|---|---|---|---|---|
| Untrusted sources and user VLANs | Internal networks | Any service not specifically approved | Deny, log, and alert | Block scans, unauthorized inbound access, and unapproved east-west traffic |

**Implementation Notes:**

- Place approved business rules above the deny rule.
- Allow internet users to reach only required DMZ services.
- Deny direct access from untrusted networks to HR and Finance.
- Deny HR-to-Finance SMB unless a documented business need exists.
- Forward deny logs to the SIEM.

---

### Proposed Rule 2: Replace Unrestricted Outbound Access

| Source | Destination | Service | Action | Purpose |
|---|---|---|---|---|
| Internal networks | External networks | Any service not specifically approved | Deny, log, and alert | Block command-and-control traffic, data exfiltration, and unapproved outbound access |

**Implementation Notes:**

- Allow approved HTTPS traffic through a secure proxy or filtering service.
- Require internal clients to use corporate DNS servers.
- Block direct external DNS from user systems.
- Deny all other outbound traffic unless approved.
- Add IDS signatures for known malicious domains and IP addresses.
- Alert on unusual outbound ports, destinations, or traffic patterns.

---

## Security Recommendations Summary

| Security Area | Recommended Improvement |
|---|---|
| Endpoint Detection | Use endpoint monitoring to detect malware earlier |
| Containment | Automatically quarantine high-confidence infected endpoints |
| Isolation Verification | Confirm that isolated devices cannot communicate |
| Recovery | Validate backups and check for persistence before restoration |
| Post-Incident Review | Require a review after every significant malware incident |
| Public Services | Place web and mail servers in a DMZ |
| Internal Segmentation | Separate HR and Finance with VLANs and firewall rules |
| Firewall Management | Restrict administration to approved systems and require MFA |
| Inbound Filtering | Replace broad allow rules with default-deny controls |
| Egress Filtering | Permit only approved outbound services |
| DNS Security | Require internal DNS and block direct external DNS |
| IDS Monitoring | Alert on command-and-control, scanning, lateral movement, and malicious domains |

---

## Project Files

Because this project is a written security assessment, the repository does not require screenshots or an images folder.

Suggested repository structure:

```text
FinSecure-Incident-and-Network-Security-Review/
│
├── README.md
└── docs/
    ├── FinSecure-Incident-and-Network-Security-Review.pdf
    └── Incident-and-Network-Security-Artifacts.pdf
```

### Documentation

- **[Full Incident and Network Security Review](https://github.com/user-attachments/files/30112960/FinSecure.Corp.Incident.and.Network.Security.Review.Task.2.docx)**
- **[Incident and Network Security Artifacts](https://github.com/user-attachments/files/30112961/Incident.and.Network.Security.Artifacts.docx)**

---

## Key Takeaways

This assessment shows that detecting malware is only one part of incident response. FinSecure also needs faster endpoint detection, confirmed isolation, validated recovery, and mandatory post-incident reviews.

The network review also shows how broad firewall rules and weak segmentation can allow malware to communicate externally and move between departments. A DMZ, internal segmentation, restricted management access, controlled egress, and default-deny rules would provide stronger protection for FinSecure's systems and data.

---

## References

- Nelson, A., Rekhi, S., Scarfone, K., & Souppaya, M. (2025). *Incident Response Recommendations and Considerations for Cybersecurity Risk Management: A CSF 2.0 Community Profile*. National Institute of Standards and Technology.
- Scarfone, K., & Hoffman, P. (2009). *Guidelines on Firewalls and Firewall Policy*. NIST Special Publication 800-41 Revision 1.
- Scarfone, K., & Mell, P. (2007). *Guide to Intrusion Detection and Prevention Systems*. NIST Special Publication 800-94.
- Western Governors University. *C845 Task 2: Incident and Network Security Artifacts*.

---

## Author

**Dontrell Wilson**  
Cybersecurity and Information Assurance Student  
CompTIA A+ | Network+ | Security+ | ITIL 4 Foundation
