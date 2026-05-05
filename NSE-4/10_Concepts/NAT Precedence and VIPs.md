---
tags: [nse4, fortigate, concept, nat, vip, routing]
date: 2026-05-05
status: complete
---
---
tags: [nse4, fortigate, concept, nat, vip, routing]
date: 2026-05-05
status: complete
---
# Concept: NAT Precedence and VIPs

## 🧠 Overview: SNAT vs DNAT
Network Address Translation (NAT) is essential for bridging private internal networks with the public internet. FortiOS handles two primary types of NAT within its firewall policies:

* **SNAT (Source NAT):** Modifies the *source* IP address of a packet. This is typically used for outbound traffic (Internal to Internet) so that private IP addresses are hidden behind a public IP address.
* **DNAT (Destination NAT):** Modifies the *destination* IP address of a packet. This is typically used for inbound traffic (Internet to Internal) to allow external users to reach a private internal server.

> **Crucial UI Distinction:** In a standard FortiOS firewall policy, the toggle switch simply labeled **"NAT"** *only* controls SNAT. Destination NAT is handled entirely by placing a **Virtual IP (VIP)** object in the destination field of the policy.

---

## 🎯 Virtual IPs (VIPs) & DNAT
A Virtual IP (VIP) is Fortinet's mechanism for configuring Destination NAT. It maps an external public IP address to an internal private IP address.
* **Static NAT (One-to-One):** Maps one public IP to exactly one private IP. All ports are forwarded.
* **Port Forwarding:** Maps a specific external port on a public IP to a specific internal port on a private IP (e.g., External IP 100.65.0.200:8080 maps to Internal Server 10.0.11.50:80).

---

## 🔀 The Static VIP Quirk (Bidirectional NAT)
By default, when you create a standard Static NAT VIP (one-to-one without port forwarding), FortiOS assumes you want that server to maintain a consistent identity on the internet. 

Because of this, FortiOS automatically applies the VIP translation in **both directions**:
1. **Inbound (DNAT):** External users hitting the public IP are translated to the private server.
2. **Outbound (SNAT):** When that private server initiates its own outbound connection to the internet, FortiOS translates its source IP to the VIP's public IP, overriding the default behavior of using the egress interface's IP.

---

## ⚖️ SNAT Order of Precedence (The Hierarchy)
When an internal host sends outbound traffic, FortiOS must decide which public IP to use for translation. In **Policy-Based NAT** (the default mode), it evaluates the available options based on a strict hierarchy from highest priority to lowest:

### 1. Dynamic IP Pool (Highest Priority)
If the firewall policy matching the traffic has "Use Dynamic IP Pool" selected and a pool is assigned, FortiOS will **always** use the addresses in that pool. This explicitly overrides any VIP mappings the host might have.

### 2. Static NAT VIP (Middle Priority)
If no IP Pool is assigned to the policy, FortiOS checks if the source IP of the internal host matches the internal IP of an existing Static VIP. If a match is found, FortiOS uses the VIP's external IP address for SNAT. 
*(Note: VIPs configured with Port Forwarding do not trigger this behavior; they only act as DNAT).*

### 3. Outgoing Interface Address (Lowest Priority / Default)
If no IP pool is applied, and the host is not mapped to a Static VIP, FortiOS falls back to the default behavior: it translates the source IP to the primary IP address of the firewall's egress (outgoing) interface.

---

## 🪃 VIP Hairpinning (NAT Loopback)
**The Scenario:** You have an internal Web Server with a VIP. External users can reach it using the Public IP. However, when *internal* users on your LAN try to browse to that same Public IP, the connection fails.

**The Fix:** This requires a Hairpin NAT (or NAT Loopback) policy. You must create a firewall policy where:
* **Incoming Interface:** Internal (LAN)
* **Outgoing Interface:** Internal (LAN)
* **Source:** Internal Subnet
* **Destination:** The VIP Object
* **NAT:** Enabled (SNAT must be on so the server replies to the FortiGate, not directly to the client, which would cause an asymmetric routing drop).

---

## 🏛️ Policy NAT vs. Central NAT
FortiOS can operate NAT in two completely different modes. You cannot mix them; it is a global setting.

* **Policy NAT (Default):** SNAT and DNAT are configured directly inside the IPv4 Firewall Policies.
* **Central NAT:** SNAT and DNAT are stripped out of the firewall policies entirely. You configure NAT in a dedicated "Central SNAT" table. The FortiGate evaluates the Firewall Policy Table to check if the traffic is *allowed*, and then evaluates the Central NAT table to see how it should be *translated*. This is heavily preferred in massive enterprise environments with thousands of rules.

---

## 🚨 NSE 4 Exam Tips
* **Scenario Questions:** You will likely see a question showing an internal host with a VIP assigned, but the outbound policy uses an IP Pool. You must know that the traffic will exit using the **IP Pool** address because it has higher precedence.
* Remember that disabling the "NAT" toggle on an outbound policy disables SNAT entirely, meaning the traffic will leave the FortiGate with its original private IP address (and likely be dropped by the ISP).
* Understand why **Hairpin NAT** requires the NAT toggle (SNAT) to be enabled on the LAN-to-LAN policy to prevent asymmetric routing.

---
## 🔗 Related Notes
* [[LAB 03 - Firewall Policies and NAT]]
* [[Firewall Policy Processing]]