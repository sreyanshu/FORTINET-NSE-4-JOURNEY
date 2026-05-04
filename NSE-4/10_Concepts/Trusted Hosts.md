
**Tags:** #Concept #Security #Administration

## Overview
**Trusted Hosts** is a security feature used to restrict the network locations from which a specific administrator can log into the FortiGate. When configured, the FortiGate will only accept login attempts for that specific admin account if the source IP address matches the defined trusted subnet(s).

## Key Takeaways
## Key Takeaways for NSE 4 
*  **Security Best Practice:** It prevents unauthorized management access from external or untrusted internal networks, even if the attacker has the correct username and password. 
*  **Per-Admin Basis:** Trusted hosts are configured on the individual administrator account, *not* globally or on the Administrator Profile.
*  **Capacity:** You can configure up to 10 IPv4 trusted hosts and up to 10 IPv6 trusted hosts per administrator account. 
*  **Default Behavior:** If no trusted hosts are defined (default is `0.0.0.0/0`), the admin can log in from any IP address (provided the interface allows administrative access like HTTPS or SSH).
*
## Related Commands 
```fortios 
config system admin
	edit <admin_username> 
	set trusthost1 <IP/Subnet> 
	set trusthost2 <IP/Subnet> 
end
```

**Related Labs:**
* [[Lab 1 - System and Network Settings]]