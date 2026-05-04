---
tags: [nse4, fortigate, cli, logging, troubleshooting]
date: 2026-05-04
status: complete
---
# CLI: Diagnose Log Test

## 🧠 Overview
The `diagnose log test` command is a built-in troubleshooting tool used to generate a batch of simulated log entries across multiple log categories (Traffic, Web Filter, IPS, Antivirus, System, etc.). 

Because waiting for an actual network event (like a virus detection or an IPS signature match) to occur naturally can be difficult or dangerous, this command allows administrators to safely flood the log pipeline with dummy data on demand.

---

## 💻 The Command
```fortios
diagnose log test
```

Note: This command does not require any additional arguments. Pressing Enter immediately generates the logs.

## 🛠️ How It Works (Under the Hood)

When you execute this command, FortiOS does **not** generate any actual network traffic, and it does **not** trigger any real security profiles.

Instead, it injects predefined, hardcoded log messages directly into the FortiGate's logging daemon (`miglogd`). These dummy logs are then processed by the FortiGate exactly as if they were real:

1. They are written to the local disk/memory (if configured).
    
2. They are forwarded over the network to external servers (FortiAnalyzer, Syslog, FortiCloud) based on your Log Settings.
    

---

## 🎯 Primary Use Cases

1. **Verifying FortiAnalyzer / Syslog Connectivity:** Once you configure a remote logging server, running this command is the fastest way to prove that the routing, firewall policies, and OFTP/Syslog tunnels are working correctly. If the test logs appear on the FAZ, your connection is solid.
    
2. **Testing Log Filters:** If you configured a filter on the FortiGate to _only_ send Security logs and drop Traffic logs, you can run this command to verify that the FortiAnalyzer only receives the security-related dummy logs.
    
3. **Alert & Automation Testing:** If you have FortiGate configured to send an email alert when an IPS event occurs, running this command will trigger that email, allowing you to test your SMTP settings without needing to launch a real exploit at your firewall.
    

---

## 🚨 NSE 4 Exam Tips

- The most important thing to remember for the exam is that `diagnose log test` **simulates log messages, not traffic.** It cannot be used to test routing, firewall policies, or physical interfaces.
    
- If a question asks how to verify that a newly registered FortiAnalyzer is successfully receiving logs _without_ impacting production traffic, this command is the correct answer.
    

---

## 🔗 Related Notes

- [[LAB 02 - Logging and Monitoring]]
    
- [[FortiAnalyzer Integration]]