---
tags: [nse4, fortigate, concept, fortianalyzer, logging]
date: 2026-05-04
status: complete
---
# Concept: FortiAnalyzer Integration

## 🧠 Overview
While FortiGate devices can store logs locally (to memory or an internal disk), this is not recommended for long-term use. Local logging consumes CPU, memory, and causes wear and tear on flash drives. **FortiAnalyzer (FAZ)** is Fortinet's dedicated logging, analytics, and reporting platform. 

Integrating FortiGate with FortiAnalyzer centralizes the management plane, allowing you to correlate data across multiple firewalls in your Security Fabric.

---

## ⚙️ Core Functions of FortiAnalyzer
When integrated, FortiAnalyzer takes three major workloads off the FortiGate's shoulders:
1. **Log Aggregation & Archiving:** Secure, long-term retention of raw logs to meet compliance requirements (PCI-DSS, HIPAA, etc.).
2. **Analytics & Correlation:** Indexing logs into a SQL database so administrators can quickly search, filter, and track down security incidents in real-time.
3. **Reporting:** Generating automated, visual PDF/HTML reports based on bandwidth usage, blocked threats, and user activity.

---

## 🤝 The Registration Handshake
To prevent rogue devices from sending garbage data to your FortiAnalyzer, Fortinet uses a strict, two-step authorization process. There are two ways to complete this handshake:

### Method 1: The "Push" (Device-Initiated)
*This is the method used in [[LAB 02 - Logging and Monitoring]].*
1. **Initiate:** The FortiGate is configured with the FAZ IP address and sends its Serial Number to the FortiAnalyzer.
2. **Pending State:** The FortiGate appears in the FortiAnalyzer's "Unauthorized Devices" list. It is blocked from sending logs.
3. **Authorization:** The FAZ Administrator manually approves the FortiGate and assigns it to an ADOM.

### Method 2: The "Pull" (FAZ-Initiated)
1. **Initiate:** The FAZ Administrator uses the *Add Device Wizard* on FortiAnalyzer, typing in the FortiGate's IP address and Admin credentials.
2. **Verification:** FortiAnalyzer reaches out to the FortiGate to verify the credentials.
3. **Authorization:** Once verified, the devices are linked instantly.

---

## 📡 Log Transmission Settings
When configuring the FortiGate to send logs, you must choose an **Upload Option**. This dictates *how* and *when* logs are sent over the network.

* **Real Time (Default/Recommended):** Logs are sent to the FAZ immediately as they are generated. This uses slightly more continuous bandwidth but ensures your analytics dashboards are always up to the second.
* **Every Minute:** Logs are buffered on the FortiGate and sent in batches every 60 seconds.
* **Store and Forward:** Logs are saved locally on the FortiGate and sent to the FAZ only at a specific time of day (e.g., Midnight). This is highly useful for branch sites with very limited daytime bandwidth.

> **Technical Note:** FortiGate communicates with FortiAnalyzer using the proprietary **OFTP (Optimized Fabric Transfer Protocol)**. OFTP uses **TCP port 514** by default. If you enable "Verify FortiAnalyzer certificate", the OFTP traffic is securely encrypted using SSL/TLS.

---

## 🏢 What is an ADOM?
During registration, you must assign the FortiGate to an **ADOM (Administrative Domain)**. 
* Think of ADOMs like "Virtual FortiAnalyzers". 
* They allow you to segment data. For example, an MSSP can have "Customer A" in one ADOM, and "Customer B" in another. Customer A's admins will only ever see Customer A's logs.
* By default, a newly authorized FortiGate is placed into the global `root` ADOM.

---

## 🚨 NSE 4 Exam Tips
* Remember that **local memory logging** is erased upon a FortiGate reboot. Always use an external server (like FAZ) for persistent logs.
* If a FortiGate is registered but logs aren't showing up, check that **TCP port 514** isn't blocked by an intermediate router.
* Know the difference between **Real-Time** and **Store-and-Forward** upload options.

---
## 🔗 Related Notes
* [[LAB 02 - Logging and Monitoring]]
* [[Analytics vs Archive Logs]]