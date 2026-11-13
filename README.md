# SignDrop exposure check for BigFix

A read-only Analysis that checks whether Windows has the CLFS authentication
setting targeted by the published SignDrop exploit. It reports exactly two
properties: **Vulnerable** and **CLFS Authentication Mode**.

## What makes a system vulnerable?

CLFS is a Windows logging component. Its authentication feature checks that log
files have not been tampered with. SignDrop abuses part of that feature: after
an attacker can run code locally, a privileged process can be tricked into
creating a file the attacker controls. This can lead to SYSTEM access—the
highest local Windows privileges.

The published attack targets authentication modes **0 and 1**. This Analysis
checks that setting; it does not run the exploit or prove that an attack has
succeeded. It applies to Windows 11 25H2 or later and Windows Server 2025.

## Import and activate

1. [Download the Analysis](<Results Analysis/SignDrop-CLFS-Authentication-Exposure-V1.0.bes>) using GitHub's **Download raw file** button.
2. In the BigFix Console, select **File → Import**, choose the `.bes` file, and select your custom site.
3. Open **Analyses**, find **SignDrop - CLFS Authentication Exposure V1.0**, right-click it and select **Activate**.
4. Open its **Results** tab. Allow time for applicable clients to gather the content, evaluate the properties and report. Both properties have a 15-minute evaluation period; offline systems must reconnect.

No Task or Action needs to be run.

## Interpret results

| Vulnerable | CLFS Authentication Mode | Meaning |
| --- | --- | --- |
| True | 0 | Authentication is enforced; the published SignDrop condition is present. |
| True | 1 | Authentication is in learning mode; the published SignDrop condition is present. |
| False | 2 | Authentication was disabled by an administrator. |
| False | 3 | Authentication was disabled automatically by Windows. |
| Unavailable / error | Unavailable / error | The value could not be read or is invalid. Exposure is unknown. |

**Do not disable CLFS authentication to obtain False.** Modes 2 and 3 turn off
an existing security protection. False means the published SignDrop condition
was not found; it does not mean the system is fully protected. Older Windows
versions and other operating systems are not applicable to this Analysis.

The checked value is `Mode` under
`HKLM\SYSTEM\CurrentControlSet\Services\CLFS\Authentication`.
This Analysis changes no settings and does not detect past exploitation.
It does not check whether a Windows update has fixed the flaw.

## References

- [Microsoft: CLFS authentication and mode values](https://support.microsoft.com/en-us/servicing/os/windows/2025/03/common-log-file-system-clfs-authentication-mitigation)
- [SignDrop research](https://github.com/MSNightmare/SignDrop/tree/87a33cd4abc90b158e13220741eca2a54b44eb74)

Licensed under [Apache License 2.0](LICENSE).
