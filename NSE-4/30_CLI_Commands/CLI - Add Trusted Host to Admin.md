
**Tags:** #CLI #Admin #TrustedHosts #FortiOS7_6

## Overview
This command adds a secondary trusted subnet to an existing administrator account. It is used when an administrator needs management access from multiple distinct network segments.

## The Command
```fortios
config system admin
    edit Security
    set trusthost2 10.0.11.0/24
end
```
