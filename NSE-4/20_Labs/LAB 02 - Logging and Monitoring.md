---
tags: [nse4, fortigate, lab, fortianalyzer, logging]
date: 2026-05-04
lab: "Lab 02 — Logging and Monitoring"
duration: 60 minutes
status: complete
---
---
tags: [nse4, fortigate, lab, fortianalyzer, logging]
date: 2026-05-04
lab: "Lab 02 — Logging and Monitoring"
duration: 60 minutes
status: complete
---
# LAB 02 - Logging and Monitoring

## 🎯 Objectives
* **Push Registration:** Configure HQ-NGFW-1 to initiate a connection request to FortiAnalyzer (bypassing the FAZ Add Device wizard).
* **Device Authorization:** Review and manually accept the FortiGate connection request on the FortiAnalyzer.
* **Log Generation:** Use the FortiOS CLI to generate safe, simulated test logs.
* **Log Analysis:** Navigate the FortiAnalyzer GUI to filter and view both real-time and historical analytics logs.

---
## 🗺️ Environment 
*Refer to [[NSE 4 Lab Topology]] for full baseline credentials.*
* **Target Firewall:** [[HQ-NGFW-1]] (`admin` / `Fortinet1!`)
* **Analytics Server:** [[HQ-FAZ-1]] (`admin` / `Fortinet1!`) - *IP: 10.0.13.125*
* **Local Test Machine:** [[HQ-PC-1]] (Using PuTTY/SSH)

---
## 🛠️ Detailed Step-by-Step Configuration

### Exercise 1: Registering Devices on FortiAnalyzer
*There are two ways to register a device: Pulling it from the FortiAnalyzer, or Pushing it from the FortiGate. In this exercise, we are using the **Push** method. Because we initiate it from the FortiGate, we do not need to use the Add Device Wizard on the FortiAnalyzer.*

#### Step 1: Initiate Log Forwarding from HQ-NGFW-1
1. Log in to the **HQ-NGFW-1** GUI as `admin`.
2. Navigate to **Security Fabric > Fabric Connectors**.
3. Locate the **Logging & Analytics** card, right-click it, and select **Edit**.
4. Click **Enabled** to turn on FortiAnalyzer integration.
5. Configure the exact connection parameters:
   * **Server:** `10.0.13.125` *(This is the IP of HQ-FAZ-1)*
   * **Upload option:** `Real Time` *(Sends logs immediately as they are generated, rather than batching them on a schedule).*
   * **Allow access to FortiGate REST API:** `Enabled`
   * **Verify FortiAnalyzer certificate:** `Enabled`
6. Click **OK**.
7. A *Verify FortiAnalyzer Serial Number* security warning will pop up. Click **Accept** to trust the FAZ certificate.
8. **CRITICAL STEP:** Right-click **Logging & Analytics** again, click **Edit**. When the *Confirm* window pops up, click **Cancel**. 
> 💡 *Why did we cancel?* If we proceed here, the FortiGate tries to finish the authorization itself. By canceling, we force the authorization to stay in a "Pending" state so we can manually approve it on the FortiAnalyzer side, practicing the Zero Trust approach.

#### Step 2: Authorize the Device on FortiAnalyzer (i.e HQ-FAZ-1)
1. Log in to the **FortiAnalyzer** GUI as `admin`.
2. Navigate to **Device Manager**.
3. Look at the top right of the screen. Click the **Notification Icon** (warning message) to display the list of unauthorized devices attempting to connect.
4. Locate your FortiGate in the list, check the box next to it, and click **Authorize**.
5. The *Authorize Device* window will open. In the **Add the following device to ADOM** field, leave the default value as `root`.
   * *(An ADOM is an Administrative Domain, used to segment data for different tenants or departments. `root` is the default global container).*
6. Click **OK**, then click **Close**.

#### Step 3: Verify the Registration Pipeline
1. **On FortiGate:** Go to **Security Fabric > Fabric Connectors**, edit *Logging & Analytics*. You should now see the *FortiAnalyzer Usage* window showing active storage stats. Click **OK**.
2. **On FortiAnalyzer:** Go to **Device Manager > All Logging Devices** and hit refresh.
> ✅ **Success Check:** You should see your FortiGate listed with a **Green Arrow** icon next to it. This indicates the logging tunnel is up and FortiAnalyzer is actively receiving data. *(Note: This can take 2-3 minutes. Keep refreshing).*

---

### Exercise 2: Generating and Viewing Logs
*We need data to look at. We will use a built-in diagnostic command to simulate traffic and security events without needing to launch actual attacks on our lab network.*

#### Step 1: Generate Test Logs via CLI
1. Open your **HQ-PC-1** VM.
2. Open **PuTTY** and connect to the saved SSH session for **HQ-NGFW-1**.
3. Log in as `admin`.
4. Run the log generation command:
   ```fortios
   diagnose log test
   ```
5. **Repeat this command 4 or 5 times** pressing the UP arrow and Enter. This ensures we have a healthy batch of dummy logs across different categories (Traffic, Web Filter, IPS, etc.) to analyze.
   
#### Step 2: Analyze Logs in FortiAnalyzer

_Note the difference in FAZ terminology: **Log View** displays analytics/indexed logs for fast searching. **Log Browse** displays raw, compressed archive logs._

1. Return to the **FortiAnalyzer** GUI.
    
2. Navigate to **Log View > Logs > Fortinet Logs**.
    
3. Click the **FortiGate** icon to filter for firewall logs, then click **Traffic**.
    
4. **Exploring Real-Time Logs:**
    
    - In the upper-right corner, click **More > Real-time Log**.
        
    - You will see logs flowing in as they happen. These are temporarily compressed but indexed immediately when CPU is available.
        
    - **Tip:** Click **Pause** at the top to freeze the screen so you can inspect a specific log entry before it scrolls away. Click **Resume** when done.
        
5. **Exploring Historical Logs:**
    
    - Click **More > Historical Log**.
        
    - This view allows you to apply complex filters (by IP, Application, Action) to query logs that have been permanently indexed and stored in the database.
        

---

## 🧠 Key Concepts & Links

- [[FortiAnalyzer Integration]]
    
- [[Analytics vs Archive Logs]]
    
- [[CLI - Diagnose Log Test]]