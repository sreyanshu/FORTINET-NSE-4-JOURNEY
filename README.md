# Fortinet NSE 4 — Network Security Certification Journey

> A hands-on lab documentation project by **Sreyanshu Epari** and **Ashutosh Maharana**
> pursuing the Fortinet NSE 4 certification at **ITER, SOA University**, supported by **Zyvka**.

---

## Collaborators

| Name | LinkedIn | Role |
|---|---|---|
| Sreyanshu Epari | [sreyanshu-epari-851a3621b](https://www.linkedin.com/in/sreyanshu-epari-851a3621b/) | Primary  |
| Ashutosh Maharana | [ashutoshmaharana004](https://www.linkedin.com/in/ashutoshmaharana004/) | Primary Collaborator |

**Institution:** ITER, SOA University
**Supported by:** Zyvka
**Certification Target:** Fortinet NSE 4 — Network Security Professional
**Total Lab Hours:** ~19.5 hours across 14 labs

---

## Lab Progress

| #   | Lab Title                                | Duration | Status      | Date Completed | Notes                                                                                                                                                                               |
| --- | ---------------------------------------- | -------- | ----------- | -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 01  | [[LAB 01 - System and Network Settings]] | 40 min   | ✅ Done      | 2026-05-02     | [View]([./labs/lab-01-system-network-settings.md](https://github.com/sreyanshu/FORTINET-NSE-4-JOURNEY/blob/main/NSE-4/20_Labs/LAB%2001%20-%20System%20and%20Network%20Settings.md)) |
| 02  | [[LAB 02 - Logging and Monitoring]]      | 60 min   | ✅ Done      | 2026-05-03     | [View]([./labs/lab-02-logging-and-monitoring.md](https://github.com/sreyanshu/FORTINET-NSE-4-JOURNEY/blob/main/NSE-4/20_Labs/LAB%2002%20-%20Logging%20and%20Monitoring.md))         |
| 03  | [[LAB 03 - Firewall Policies and NAT]]   | 120 min  | ✅ Done | —              | —                                                                                                                                                                                   |
| 04  | [[LAB 04 - Routing]]                     | 100 min  | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 05  | [[LAB 05 - Firewall Authentication]]     | 70 min   | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 06  | [[LAB 06 - FSSO Configuration]]          | 70 min   | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 07  | [[LAB 07 - Certificate Operations]]      | 90 min   | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 08  | [[LAB 08 - Antivirus]]                   | 90 min   | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 09  | [[LAB 09 - Web Filtering]]               | 100 min  | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 10  | [[LAB 10 - IPS and Application Control]] | 110 min  | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 11  | [[LAB 11 - IPsec VPN Configuration]]     | 110 min  | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 12  | [[LAB 12 - SD-WAN Configuration]]        | 100 min  | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 13  | [[LAB 13 - High Availability]]           | 110 min  | 🔜 Upcoming | —              | —                                                                                                                                                                                   |
| 14  | [[LAB 14 - Diagnostics Performance]]     | 60 min   | 🔜 Upcoming | —              | —                                                                                                                                                                                   |

**Progress: 2 / 14 labs complete**

---

## Lab 01 — System and Network Settings

**Date completed:** 2026-05-02
**Duration:** 40 minutes

### Objectives

- Create a new administrator user on FortiGate
- Restrict administrator access using permission profiles
- Test administrator access across multiple VMs

### Lab Environment

| VM | Username | Role |
|---|---|---|
| HQ-PC-1 | Administrator | HQ Client Machine |
| BR1-PC-1 | Administrator | Branch Client Machine |
| HQ-NGFW-1 | admin | HQ FortiGate Firewall |
| BR1-FGT | admin | Branch FortiGate |
| HQ-FAZ-1 | admin | FortiAnalyzer |

### What We Did

1. Logged into HQ-NGFW-1 using the default admin credentials.
2. Navigated to System > Administrators and created a new admin account with restricted permissions.
3. Configured a custom admin profile limiting access to specific VDOM features.
4. Tested the new account login from HQ-PC-1 and verified restricted areas were inaccessible.
5. Observed how FortiAnalyzer (HQ-FAZ-1) integrates with FortiGate for centralized admin logging.

### Key Takeaways

- FortiGate separates authentication (who you are) from authorization (what you can do) through admin profiles.
- FortiAnalyzer acts as a centralized log and management hub — changes made on FortiGate are reflected there.
- Restricting admin access is a zero-trust principle applied at the management plane itself.

### Issues Encountered

- None. Straightforward lab to start the journey.

---

## Lab 02 — Logging and Monitoring

**Date completed:** 2026-05-03
**Duration:** 60 minutes

### Objectives

- Configure logging on FortiGate to capture traffic, events, and security incidents
- Forward logs to FortiAnalyzer for centralized storage and management
- Use the Log & Report dashboard to read and interpret firewall logs
- Understand the difference between local logging and remote logging

### Lab Environment

| VM | Username | Role |
|---|---|---|
| HQ-PC-1 | Administrator | HQ Client Machine |
| BR1-PC-1 | Administrator | Branch Client Machine |
| HQ-NGFW-1 | admin | HQ FortiGate Firewall |
| BR1-FGT | admin | Branch FortiGate |
| HQ-FAZ-1 | admin | FortiAnalyzer |

### What We Did

1. Enabled logging on HQ-NGFW-1 and configured log severity levels for traffic and security events.
2. Set FortiAnalyzer (HQ-FAZ-1) as the remote log destination and verified the connection.
3. Navigated to Log & Report on FortiGate and filtered logs by type — traffic, event, and security.
4. Generated test traffic from HQ-PC-1 to BR1-PC-1 and confirmed the logs were captured on both local storage and FAZ.
5. Compared local disk logging vs. FAZ logging — retention limits, query speed, and compliance use cases.
6. Reviewed log fields: source/destination IP, policy ID, action (allow/deny), and session duration.

### Key Takeaways

- Logs are not just a diagnostic tool — in a real SOC, they are evidence. Every policy match, dropped packet, and failed login is recorded and used to reconstruct incidents.
- FortiAnalyzer centralizes logs from multiple FortiGate devices (HQ + branch), making it the single source of truth for audit and compliance.
- Local logging on FortiGate has limited retention — enterprises rely on FAZ for long-term log storage and correlation across sites.
- Understanding log fields (policy ID, action, source/dest) is a foundational skill for any network security analyst role.

### Issues Encountered

- None. Log forwarding to FAZ connected cleanly once the device was registered.

---

## Obsidian Notes Structure

We maintain a parallel Obsidian vault for richer, interlinked notes alongside this repository.

```
NSE4-Vault/
├── 00-Overview/
│   ├── Certification-Roadmap.md
│   ├── Glossary.md
│   └── VM-Lab-Environment.md
├── Labs/
│   ├── Lab-01-System-Network-Settings.md      ✅
│   ├── Lab-02-Logging-and-Monitoring.md       ✅
│   ├── Lab-03-Firewall-Policies-and-NAT.md
│   ├── Lab-04-Routing.md
│   ├── Lab-05-Firewall-Authentication.md
│   ├── Lab-06-FSSO-Configuration.md
│   ├── Lab-07-Certificate-Operations.md
│   ├── Lab-08-Antivirus.md
│   ├── Lab-09-Web-Filtering.md
│   ├── Lab-10-IPS-Application-Control.md
│   ├── Lab-11-IPsec-VPN-Configuration.md
│   ├── Lab-12-SD-WAN-Configuration.md
│   ├── Lab-13-High-Availability.md
│   └── Lab-14-Diagnostics-Performance.md
├── Concepts/
│   ├── FortiGate-Architecture.md
│   ├── Admin-Profiles.md
│   ├── NAT-Types.md
│   ├── Routing-Protocols.md
│   ├── VPN-Types.md
│   ├── Security-Profiles.md
│   └── FortiAnalyzer-Integration.md
├── Diagrams/
│   └── HQ-Branch-Topology.canvas
└── Daily-Log/
    ├── 2026-05-02.md
    └── ...
```

### Obsidian Lab Note Template

Each lab note in the vault follows this structure:

```markdown
---
tags: [nse4, fortigate, lab]
date: YYYY-MM-DD
lab: "Lab XX — Title"
duration: XX minutes
status: complete / in-progress / upcoming
---

## Objective
What this lab is designed to teach.

## Lab Environment
VMs and credentials used in this lab.

## Steps
Step-by-step walkthrough of what we did.

## Concepts Linked
[[FortiGate-Architecture]] | [[Admin-Profiles]] | [[NAT-Types]]

## Key Takeaways
What we actually learned, in plain language.

## Questions / Gaps
Anything we didn't fully understand — flagged to revisit.

## Screenshots
![[screenshot-name.png]]
```

---

## Repository Structure

```
nse4-journey/
├── README.md                              ← Master progress tracker (this file)
├── labs/
│   ├── lab-01-system-network-settings.md
│   ├── lab-02-logging-and-monitoring.md
│   └── ...                               ← Added after each lab is complete
├── concepts/
│   ├── fortigate-architecture.md
│   ├── nat-types.md
│   └── ...
└── assets/
    └── screenshots/
        ├── lab-01/
        ├── lab-02/
        └── ...
```

---

## How to Use This Repo

If you're on your own NSE 4 journey and found this repository:

- The **Lab Progress** table above shows where we are and links to each completed lab file.
- Each file under `/labs/` contains objectives, environment setup, a full walkthrough, and key takeaways.
- The `/concepts/` folder contains standalone write-ups on the theory behind each topic — useful for exam prep.
- The Obsidian vault structure above is documented if you want to replicate our note-taking approach.
- Open an issue if you want to discuss a lab or have a question.

---

## Acknowledgements

Thanks to the team at **Zyvka** for providing access to the hands-on lab environment, and to the faculty at **ITER, SOA University** for supporting students who pursue certifications alongside their academic coursework.

---

*Last updated: 2026-05-03 · Lab 02 complete · 12 labs remaining*
NSE-4/20_Labs/LAB 01 - System and Network Settings.md
https://github.com/sreyanshu/FORTINET-NSE-4-JOURNEY/blob/79e257454820e5ebc162ac3acb5433c7e653afb9/NSE-4/20_Labs/LAB%2001%20-%20System%20and%20Network%20Settings.md
NSE-4/20_Labs/LAB 01 - System and Network Settings.md
