---
tags: [nse4, fortigate, cli, troubleshooting, nat, sessions]
date: 2026-05-05
status: complete
---
# CLI: Session Management

## 🧠 Overview
FortiGate is a stateful firewall. When traffic matches a firewall policy, FortiOS creates a temporary entry in its **Session Table** in RAM. All subsequent packets belonging to that same flow are processed by looking at this session table, rather than being re-evaluated against the firewall policies. 

Understanding how to read, filter, and clear this session table is a critical troubleshooting skill.

---

## 📊 Viewing Active Sessions
To view the current active connections passing through the firewall, use the following command:

```fortios
get system session list
```

### Understanding the Output

The output of this command gives you the exact routing and NAT decisions the FortiGate made for a specific connection.

- **PROTO:** The protocol being used (e.g., `tcp`, `udp`, `icmp`).
    
- **EXPIRE:** The TTL (Time To Live) in seconds before the session times out and is removed from the table.
    
- **SOURCE:** The original IP address and port of the sender.
    
- **SOURCE-NAT:** The SNAT translation applied to the traffic (often the IP Pool or outgoing interface IP). If no SNAT is applied, this shows a dash `-`.
    
- **DESTINATION:** The original destination IP address.
    
- **DESTINATION-NAT:** The DNAT translation applied to the traffic (the internal IP mapped to a VIP).
    

---

## 🧹 Clearing Sessions (The Safe Way)

**Why clear sessions?** If you modify a NAT rule, change a routing protocol, or update a firewall policy, those changes **do not apply to active sessions**. Existing sessions will continue using the old rules until they time out naturally. To force traffic to use your new configurations immediately, you must clear the sessions.

**CRITICAL WARNING:** Never run `diagnose sys session clear` blindly. If you do not apply a filter first, this command drops _every single active connection_ on the firewall, including your own SSH or HTTPS management session!

### The 3-Step Safe Clearing Process

Always follow this exact sequence when clearing sessions to avoid dropping production traffic:

**Step 1: Reset the filter (Start clean)** Make sure no lingering filters from a previous troubleshooting session are active.

Code snippet

```
diagnose sys session filter clear
```

**Step 2: Apply a specific filter** Narrow down the target. You can filter by source IP, destination IP, port, or protocol. _(Example: Filter only traffic originating from HQ-PC-1)_

Code snippet

```
diagnose sys session filter src 10.0.11.50
```

**Step 3: Execute the clear command** Now that the filter is active, the clear command will _only_ affect the matched sessions.

Code snippet

```
diagnose sys session clear
```

---

## 🔍 Other Useful Filter Commands

You can combine multiple filters in Step 2 to be extremely precise before clearing.

- Filter by destination IP: `diagnose sys session filter dst 8.8.8.8`
    
- Filter by destination port: `diagnose sys session filter dport 443`
    
- Verify what your current filter is set to: `diagnose sys session filter` (Press Enter without arguments)
    

---

## 🚨 NSE 4 Exam Tips

- Know the 3-step sequence by heart. Exam questions will try to trick you by offering `diagnose sys session clear` as a primary troubleshooting step without mentioning filters.
    
- If a scenario states that a firewall policy was updated to block an IP, but the user can still access the server, the correct answer is almost always that the existing session needs to be cleared (or allowed to time out).
    

---

## 🔗 Related Notes

- [[LAB 03 - Firewall Policies and NAT]]
    
	- [[Firewall Policy Processing]]