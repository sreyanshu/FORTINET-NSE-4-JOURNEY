---
tags: [nse4, fortigate, concept, fortianalyzer, logging]
date: 2026-05-04
status: complete
---
# Concept: Analytics vs. Archive Logs

## 🧠 Overview
When FortiGate devices send logs to a FortiAnalyzer (FAZ), the FAZ must balance two competing priorities: **speed** (for real-time dashboarding and searching) and **storage capacity** (for long-term compliance). 

To achieve this, FortiAnalyzer processes incoming logs into two parallel pipelines: **Analytics Logs** and **Archive Logs**.

---

## 📊 Analytics Logs (The "Fast" Storage)
Analytics logs are the "active" logs used for daily administration, troubleshooting, and report generation.

* **How they are stored:** Incoming logs are processed and inserted into an actively indexed **SQL database**. 
* **The Benefit:** Because the data is indexed, searching and filtering (e.g., "show me all traffic from IP 10.0.1.53 over the last 7 days") is incredibly fast.
* **Where you see them:** When you use **Log View**, dashboards, FortiView, or generate reports in the FAZ GUI, you are pulling from the Analytics database.
* **The Trade-off:** SQL databases consume a significant amount of CPU, RAM, and fast disk space. Therefore, analytics logs are generally kept for a much shorter duration (e.g., 30 to 90 days) compared to archive logs.

---

## 🗄️ Archive Logs (The "Deep" Storage)
Archive logs are the raw, original log files kept primarily for historical reference, auditing, and legal compliance.

* **How they are stored:** Incoming raw logs are written to flat text files. Periodically, FortiAnalyzer compresses these files (usually into `.gz` format) to save disk space.
* **The Benefit:** They take up a fraction of the storage space compared to indexed SQL data, allowing you to retain logs for years (e.g., 1 to 7 years) to meet compliance requirements like PCI-DSS or HIPAA.
* **Where you see them:** You can view archive logs using the **Log Browse** feature.
* **The Trade-off:** Because they are compressed flat files and *not* indexed in a database, searching through archive logs is a very slow, manual process.

---

## ⚖️ Storage Quotas and Data Policies
FortiAnalyzer administrators must configure **Data Policies** to manage how long logs live in each state. This is based on the allocated disk quota.

* **Analytics Quota:** Defines the maximum disk space allocated for the SQL database.
* **Archive Quota:** Defines the maximum disk space allocated for compressed log files.

**What happens when the disk gets full?**
FortiAnalyzer uses a First-In, First-Out (FIFO) method. When an ADOM reaches its allocated quota, the oldest logs are automatically deleted to make room for new incoming logs.

---

## 🚨 NSE 4 Exam Tips
* Be prepared to differentiate between the FAZ GUI elements: 
    * **Log View** = Indexed Analytics logs (Fast).
    * **Log Browse** = Compressed Archive logs (Slow).
* Remember that **Analytics** data is used to generate Reports. If a log has been purged from Analytics and only exists in the Archive, you cannot run a report on it.
* Know that both types of logs are stored simultaneously when they first arrive at the FortiAnalyzer.

---
## 🔗 Related Notes
* [[LAB 02 - Logging and Monitoring]]
* [[FortiAnalyzer Integration]]