---
tags: [nse4, fortigate, concept, firewall, security-fabric]
date: 2026-05-05
status: complete
---
# Concept: Firewall Policy Processing

## 🧠 Overview
At its core, a FortiGate is a stateful firewall. This means it tracks the state of active network connections and only requires a single policy to allow bidirectional communication for an established session (e.g., if you allow a PC out to the internet, the firewall automatically tracks the session and allows the website's return traffic back in). 

However, before any session is established, the very first packet of that session must be evaluated against the **Firewall Policy Table**.

---

## 🚦 The "First Match Wins" Rule (Top-Down Processing)
FortiOS evaluates firewall policies sequentially, strictly from **top to bottom**. 

When a new packet arrives, the FortiGate checks it against the policies in the list one by one. For a policy to match, the packet must meet *all* of the following matching criteria defined in that policy:
1. **Incoming Interface**
2. **Outgoing Interface**
3. **Source** (IP/User)
4. **Destination** (IP/FQDN)
5. **Service** (Port/Protocol)
6. **Schedule** (Time of day)

**The Golden Rule:** The moment a packet matches *all* criteria of a policy, FortiOS applies the defined action (`ACCEPT` or `DENY`) and **stops evaluating**. It does not matter if a "better" or more specific policy exists further down the list; it will never be seen.

---

## 🔢 Sequence Order vs. Policy ID
This is a critical distinction for administration and exam questions.

* **Policy ID:** A unique numerical identifier assigned to a policy the moment it is created. It is permanently tied to that policy and **does not change** if you move the policy around. Policy IDs are used for logging and CLI references. *Policy IDs do not determine the order in which policies are evaluated.*
* **Sequence (Order):** The actual physical placement of the policy in the list (top-to-bottom). This is what FortiOS uses to evaluate traffic. You can change the sequence by clicking and dragging a policy in the GUI.

---

## ⛔ The Implicit Deny (Policy ID 0)
At the very bottom of the firewall policy list sits the **Implicit Deny** policy. 
* It is invisible by default in some views, but it is always there and cannot be deleted.
* Its matching criteria are effectively `Source: any`, `Destination: any`, `Service: any`. 
* Its action is always `DENY`.
* **Purpose:** If a packet travels down the entire policy list and fails to match any of your custom rules, it will hit the Implicit Deny and be dropped. This enforces a strict "Default Deny" security posture.

---

## 📐 Best Practices for Policy Ordering
Because of the top-down evaluation engine, your policy list must be organized strategically:

1. **Most Granular/Specific at the Top:** Policies targeting specific users, single IP blocks, or temporary deny rules (e.g., "Block Ping from Host A to Host B") must go first.
2. **Broad/General Access in the Middle:** Policies affecting entire subnets or allowing general internet access (e.g., "Allow Internal Subnet to HTTPS Internet") go next.
3. **Implicit Deny at the Bottom:** (Handled automatically by FortiOS).

**What happens if you reverse this?** 
If you place a general "Allow All" policy at the very top, all traffic will hit it, be accepted, and evaluation will stop. Your specific "Deny" policies underneath will become "shadowed" or "orphaned" and will never trigger.

---

## 🚨 NSE 4 Exam Tips
* Be prepared to look at a screenshot of a policy list and identify *why* a specific traffic flow is failing. Usually, it's because a broader `ACCEPT` or `DENY` policy is placed higher in the sequence than the intended granular policy.
* Remember that changing the sequence order of a policy in the GUI **does not** change its Policy ID.
* Remember that FortiGate only evaluates the *first* packet of a new session against the policy list. Subsequent packets in that same session are routed and allowed via the stateful session table (`get system session list`).

---
## 🔗 Related Notes
* [[LAB 03 - Firewall Policies and NAT]]