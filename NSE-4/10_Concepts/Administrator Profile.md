
**Tags:** #Concept #NSE4 #SystemAdministration #RBAC

## Overview
In FortiOS, an **Administrator Profile** defines the specific permissions and access levels granted to an administrator account. It acts as a Role-Based Access Control (RBAC) mechanism, dictating what an admin can view, create, modify, or delete within the FortiGate GUI and CLI.

## Key Takeaways for NSE 4
* **Default Profile:** The default `super_admin` profile cannot be modified or deleted. It has full read/write access to everything.
* **Permission Levels:** Permissions are highly granular and can be set to:
  * **None:** Hides the menu entirely from the admin's view.
  * **Read:** Allows the admin to view configurations but not change them.
  * **Read/Write:** Allows the admin to view and modify configurations.
* **Application:** When you create a new local or remote administrator account, you *must* assign it an Administrator Profile. Multiple accounts can share the same profile.

## Related Commands
```fortios
config system admin profile
```

## Related Labs:
- [[LAB 01 - System and Network Settings]]

