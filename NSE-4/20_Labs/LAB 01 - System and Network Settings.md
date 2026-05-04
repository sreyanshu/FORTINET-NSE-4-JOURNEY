# Lab 01 - System and Network Settings
**Tags:** #Lab #NSE4 #AdminProfiles #TrustedHosts
**Status:** 🟩 Completed
**Time:** 20 minutes

---
## 🎯 Objectives
* Create a new administrator user profile and account.
* Restrict administrator access using Trusted Hosts.
* Test and verify administrator access from different network segments.

---
## 🗺️ Environment & Credentials
*Refer to [[NSE 4 Lab Topology]] for full baseline credentials.*
* **Target Firewall:** [[HQ-NGFW-1]] (`admin` / `Fortinet1!`)
* **Local Test Machine:** [[HQ-PC-1]] 
* **Remote Test Machine:** [[BR1-PC-1]]

---
## 🛠️ Step-by-Step Configuration

### Exercise 1: Configuring Administrator Accounts

#### Step 1: Create an Administrator Profile
*Profiles define what a user is permitted to do on the GUI and CLI.*
1. Log into **HQ-NGFW-1** GUI as `admin`.
2. Navigate to **System > Admin Profiles**.
3. Click **Create New**.
4. **Name:** `Security_Admin_Profile`
5. **Permissions:** Click **Set All**, then select **Read**.
6. **Security Profile:** Change this specific row to **Read/Write**.
7. Click **OK**.

#### Step 2: Create a Local Administrator Account
1. Navigate to **System > Administrators**.
2. Click **Create New > Administrator**.
3. **Username:** `Security` *(Type: Local User)*
4. **Password:** `Fortinet1!`
5. **Administrator Profile:** Select `Security_Admin_Profile`.
6. Click **OK**.
> ⚠️ *Note: Admin names and passwords are case-sensitive. You cannot include characters such as `< > ( ) # "`.*

#### Step 3: Test Initial Access
1. Log out of the `admin` account.
2. Log back into the GUI as `Security` (`Fortinet1!`).
3. **Verification:** Explore the GUI. You should see that this account can *only* configure security profiles; everything else is read-only.
4. Log out.

#### Step 4: Restrict Administrator Access
*We will restrict the new admin so they can only connect from a trusted subnet.*
1. Log back into **HQ-NGFW-1** GUI as `admin` from the **HQ-PC-1** VM.
2. Navigate to **System > Administrators** and edit the `Security` account.
3. Enable **Restrict login to trusted hosts**.
4. **Trusted Host 1:** `100.65.1.0/24`
5. Click **OK**.

#### Step 5: Test the Restricted Access
1. **From HQ-PC-1:** Log out of `admin`. Try to log in as `Security`.
   * **Result:** Authentication fails. (HQ-PC-1 is connected directly and is not in the `100.65.1.0/24` subnet).
2. **From BR1-PC-1:** Log into the BR1-PC-1 VM (`Administrator` / `Fortinet1!`). Open a browser and navigate to HQ-NGFW-1 at `100.65.0.101`. Log in as `Security`.
   * **Result:** Success.
   * **Why?** The BR1-PC-1 VM accesses the firewall through port2 on BR1-FGT. That port sits inside the `100.65.1.0/24` subnet, matching our Trusted Host rule.

---
## 💻 CLI Operations
*We need to add HQ-PC-1's subnet as a second trusted host so we can manage the firewall locally as the Security admin.*

1. Open the **HQ-NGFW-1** CLI and log in as `admin`.
2. Execute the following commands to add `10.0.11.0/24` as `trusthost2`:

```fortios
config system admin
    edit Security
    set trusthost2 10.0.11.0/24
end
```

3. **Final Verification:** Return to the HQ-PC-1 VM. Open a browser and log in to the GUI at `10.0.11.254` as `Security`. You will now be able to log in successfully.

## Key Concepts & Links

- [[Administrator Profile]]
    
- [[Trusted Hosts]]