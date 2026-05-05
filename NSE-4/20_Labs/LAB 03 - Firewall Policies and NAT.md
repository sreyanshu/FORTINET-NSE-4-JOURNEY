---
tags: [nse4, fortigate, lab, firewall, nat, vip]
date: 2026-05-04
lab: "Lab 03 — Firewall Policies and NAT"
duration: 120 minutes
status: complete
---
# LAB 03 - Firewall Policies and NAT

## 🎯 Objectives
* **Address Objects:** Create reusable Firewall Address Objects to simplify policy management.
* **Access Control:** Configure IPv4 firewall policies with both `ACCEPT` and `DENY` actions.
* **Top-Down Processing:** Understand and physically test how FortiOS evaluates policies from top to bottom.
* **Destination NAT (DNAT):** Configure Virtual IPs (VIPs) to allow external users to access an internal server.
* **Source NAT (SNAT):** Configure Dynamic IP Pools and observe how they override default NAT behaviors.

---
## 🗺️ Environment 
* **Target Firewall:** [[HQ-NGFW-1]] (`admin` / `Fortinet1!`)
* **Local Test Machine:** [[HQ-PC-1]] (Internal IP: `10.0.11.50`)
* **Remote Test Machine:** BR1-PC-1 (External testing via Linux Router)
* **External Test Destination:** `100.65.0.254` (LINUX_ETH2)

---
## 🛠️ Detailed Step-by-Step Configuration

### Exercise 1: Creating Firewall Address Objects and Policies
*Using address objects instead of typing raw IP addresses into policies prevents typos and allows you to update an IP in one place and have it automatically apply to all policies referencing it.*

#### Step 1: Create a Firewall Address Object
1. Log in to the **HQ-NGFW-1** GUI.
2. Navigate to **Policy & Objects > Addresses** and click **Create new > Address**.
3. Configure the object to represent your internal network:
   * **Name:** `HQ_SUBNET`
   * **Type:** `Subnet`
   * **IP/Netmask:** `10.0.11.0/24`
   * **Interface:** `any` *(Leaving this as 'any' allows the object to be used on any interface).*
4. Click **OK**.

#### Step 2: Create a General Access Firewall Policy
1. Go to **Policy & Objects > Firewall Policy**.
2. *Housekeeping:* Expand the `port4 → port2` section. Right-click the existing `Internet` policy, and select **Disable** in the Set Status field. *(We are building a better one).*
3. Click **Create New** to build our outbound internet policy:
   * **Name:** `Internet_Access`
   * **Incoming interface:** `port4` (Internal)
   * **Outgoing interface:** `port2` (External)
   * **Source:** `HQ_SUBNET`
   * **Destination:** `all`
   * **Schedule:** `always`
   * **Service:** Add `ALL_ICMP`, `HTTP`, `HTTPS`, `DNS`, `SSH`
   * **Action:** `ACCEPT`
   * **NAT:** Ensure it is `Enabled` (Uses Outgoing Interface Address).
   * **Log Allowed Traffic:** Select `All Sessions`
   * **Generate Logs when Session Starts:** `Enabled`
> 💡 *Note on Session Start Logging:* Enabling this generates two logs per connection (a Start log and an End log with the total bytes). Use this for troubleshooting, but leave it disabled in production to save disk space!
4. Click **OK**.

#### Step 3: Test Policy and View Logs
1. Open the **HQ-PC-1 VM**, launch a web browser, and visit external sites (e.g., google.com, bbc.com).
2. Return to **HQ-NGFW-1**, go to **Policy & Objects > Firewall Policy**.
3. Right-click your `Internet_Access` policy and click **Show matching logs**.
4. **Observation:** You should see logs with `Accept (Start)` in the Result column, verifying traffic is flowing.
5. *Optional:* Go to **Log & Report > Forward Traffic**, and change the log location in the top right from `Disk` to `FortiAnalyzer` to view the indexed logs.

---

### Exercise 2: Reordering Firewall Policies
*FortiOS is a stateful firewall that evaluates policies sequentially from the top down. The first policy that matches the traffic wins, and evaluation stops instantly.*

#### Step 1: Create a Granular DENY Policy
1. Go to **Policy & Objects > Firewall Policy** and click **Create New**.
2. We want to block ping to a specific server, but allow everything else:
   * **Name:** `Block_Ping`
   * **Incoming interface:** `port4`
   * **Outgoing interface:** `port2`
   * **Source:** `HQ_SUBNET`
   * **Destination:** `LINUX_ETH2` *(A pre-configured object for 100.65.0.254/32)*
   * **Service:** `PING`
   * **Action:** `DENY`
   * **Log Violation Traffic:** `Enabled`
3. Click **OK**.

#### Step 2: Test Top-Down Processing Behavior
1. On **HQ-PC-1**, open a terminal and run a continuous ping: `ping 100.65.0.254`
2. ❌ **Observation 1:** The ping *succeeds*! Why? Because the general `Internet_Access` policy is currently *above* our new `Block_Ping` policy. The traffic hits the `ACCEPT` rule first, and the FortiGate stops looking.
3. Leave the ping running. Go back to the **HQ-NGFW-1** GUI.
4. In the policy list, click the far-left column of the `Block_Ping` policy and **drag it above** the `Internet_Access` policy.
5. ✅ **Observation 2:** Look at your terminal. The continuous ping immediately starts dropping/failing! The granular policy is now evaluated first.
> 🧠 **Exam Tip:** Always place your most granular/specific policies at the top, and your general "allow all" policies at the bottom.

---

### Exercise 3: Configuring DNAT Settings Using a VIP
*A Virtual IP (VIP) is used to map a public IP address (External) to a private IP address (Internal), allowing external users to access internal resources like a web server.*

#### Step 1: Create a Virtual IP (VIP)
1. Navigate to **Policy & Objects > Virtual IPs** and click **Create New > Virtual IP**.
2. Configure the mapping:
   * **Name:** `VIP-INTERNAL-HOST`
   * **Interface:** `port2` (The external-facing interface)
   * **External IP address/range:** `100.65.0.200` (The public IP you want users to hit)
   * **Map to IPv4 address/range:** `10.0.11.50` (The internal IP of HQ-PC-1)
3. Click **OK**.

#### Step 2: Create an Ingress Policy for the VIP
1. Go to **Policy & Objects > Firewall Policy** and click **Create New**.
2. Configure the inbound policy to allow the internet to reach your VIP:
   * **Name:** `Web-Server-Access`
   * **Incoming interface:** `port2`
   * **Outgoing interface:** `port4`
   * **Source:** `all`
   * **Destination:** `VIP-INTERNAL-HOST` *(Found under the VIRTUAL IP/SERVER section)*
   * **Service:** `HTTP`, `HTTPS`
   * **Action:** `ACCEPT`
   * **NAT:** **Disabled** 
   * **Log allowed traffic:** `All sessions`
> 💡 *Why is NAT disabled here?* In a policy where the destination is a VIP, the VIP object automatically handles the Destination NAT (DNAT). Enabling the NAT toggle in the GUI turns on *Source* NAT (SNAT), which we do not want for inbound web traffic.
3. Click **OK**.

#### Step 3: Test DNAT and Default SNAT Behavior
1. On the external **BR1-PC-1 VM**, open a browser and navigate to `http://100.65.0.200`. You should see the test web page load successfully.
2. Log into the **HQ-NGFW-1 CLI** (via PuTTY or the GUI console) and run: `get system session list`. Look for the entry where `100.65.0.200` translates to `10.0.11.50`. This proves DNAT is working.
3. **The Static NAT Quirk:** On the internal **HQ-PC-1 VM**, browse to `yahoo.com`. Run `get system session list` on the FortiGate again. 
   * **Observation:** Outgoing traffic from HQ-PC-1 is being translated to the VIP's external address (`100.65.0.200`), *not* the port2 egress IP (`100.65.0.101`). When you create a VIP, FortiOS uses that VIP for *both* DNAT (inbound) and SNAT (outbound) by default for that specific host!

---

### Exercise 4: Using Dynamic NAT With IP Pools
*We want to stop HQ-PC-1 from using its VIP address for outbound internet traffic, and instead force it to use a specific pool of public IP addresses.*

#### Step 1: Create an IP Pool
1. Navigate to **Policy & Objects > IP Pools** and click **Create New > IPv4 Pool**.
2. Define a pool of public IPs available on the external segment:
   * **Name:** `INTERNAL-HOST-EXT-IP`
   * **Type:** `Overload` (Allows multiple internal IPs to share this single public IP via Port Translation).
   * **External IP Range:** `100.65.0.100 - 100.65.0.100`
3. Click **OK**.

#### Step 2: Apply IP Pool to Policy
1. Go to **Policy & Objects > Firewall Policy**.
2. Right-click the disabled `Internet` policy (from Exercise 1) and click **Enable**.
3. Edit the `Internet` policy:
   * Ensure **NAT** is `Enabled`.
   * **IP pool configuration:** Select `Use Dynamic IP Pool`.
   * Click the **+** sign and select the `INTERNAL-HOST-EXT-IP` pool.
4. Click **OK**.

#### Step 3: Clear Sessions and Test NAT Precedence
*Active sessions in FortiOS cache their routing and NAT decisions. To force the FortiGate to apply our new IP pool, we must clear the old sessions.*

1. Open the **HQ-NGFW-1 CLI** and safely clear the sessions for HQ-PC-1:
   ```fortios
   diagnose sys session filter clear
   diagnose sys session filter src 10.0.11.50
   diagnose sys session clear
   ```
   ⚠️ _Danger:_ Never run `diagnose sys session clear` without a filter, or you will drop every connection on the firewall, including your own SSH/GUI management session!
2. On **HQ-PC-1**, open new browser tabs and visit external sites to generate fresh traffic.
    
3. Back on the CLI, run `get system session list`.
    
4. **Observation:** Look at the `SOURCE-NAT` column. The SNAT address is now `100.65.0.100`.
    

> 🧠 **Exam Tip - NAT Precedence:** An explicit Dynamic IP Pool assigned to a policy **overrides** the default Static NAT VIP behavior for outgoing traffic.

---

## 🧠 Key Concepts & Links

- [[Firewall Policy Processing]]
    
- [[NAT Precedence and VIPs]]
    
- [[CLI - Session Management]]
