---
hidden: true
---

# HP ZBook Firefly 14 inch G11 - UEFI Protection Bypass via Windows Recovery Environment (WinRE)

**Author:** Beatriz Fresno Naumova (beafn28)

**Date:** the date when the CVE is published

**Vendor:** HP

**Product:** HP ZBook Firefly 14 inch G11 (UEFI/BIOS)

**Model:** ZBook Firefly 14 inch G11

**Component:** UEFI Administrator Authentication Policies / External Boot Restrictions

**Attack type:** Local

**Impact:** Security Bypass / Unauthorized Boot

**Status:** Reported to vendor

**CVE:** Not yet published

### Summary

HP systems that rely on UEFI administrator password protection and Administrator Authentication Policies to restrict booting from external or legacy devices may be vulnerable to a security bypass under common default configurations.

When Windows Recovery Environment (WinRE) is enabled, booting from an external device can be performed without providing the configured UEFI administrator password. This weakens the intended security model of UEFI boot protection and may allow unauthorized operating systems to be executed on affected systems.

### Affected Configuration

* HP systems with **UEFI administrator password enabled**
* **Administrator Authentication Policies** configured to restrict external or legacy boot devices
* Microsoft Windows installed with **default recovery options enabled (WinRE)**

### Tested Environment&#x20;

> The following configuration was used to reproduce the behavior:

* **Device / Model:** HP ZBook Firefly 14 inch G11
* **Platform:** Laptop workstation
* **Firmware Interface:** UEFI
* **BIOS Version:** W70 Ver. 01.06.10
* **Operating System:** Windows 11 (default configuration)
* **WinRE:** Enabled
* **UEFI Administrator Password:** Enabled
* **Boot Restrictions:** External/legacy boot devices configured to require administrator authentication (Administrator Authentication Policies)

### Vulnerability Details

When a UEFI administrator password is configured, direct access to the one-time boot menu (commonly via the **F9** key) correctly enforces administrator authentication before allowing boot from restricted devices.

However, if Windows is already running, any local user can access the Windows Recovery Environment (for example, by holding the **Shift** key during a system restart). Within WinRE, the option **“Use a device”** allows selecting an external boot medium such as a USB flash drive.

When this option is used, the system boots from the selected external device **without prompting for the UEFI administrator password**, effectively bypassing the configured UEFI boot protection and Administrator Authentication Policies.

### Security Impact

An attacker with local access could:

* Boot an unauthorized operating system from external media
* Bypass OS-level security controls and endpoint protection mechanisms
* Potentially access or manipulate local storage (depending on disk encryption status)
* Perform offline attacks (credential extraction, forensic evasion) or data exfiltration

This issue is particularly relevant in enterprise, government, and high-assurance environments where UEFI boot protection is relied upon as a defense against unauthorized system modification.

### Proof of Concept

1. Boot into Windows using any local user account.
2. Trigger a system restart while holding the **Shift** key to enter WinRE.
3. Select **“Use a device”**.
4. Choose an external boot medium (e.g., USB flash drive).
5. Observe that the system boots from the external device **without requesting the UEFI administrator password**.

#### CVSS Details

**CVSS v4.0 Vector:**<br>

**Base Score:** 4.8 (Medium)

### Mitigation

At the time of writing, no firmware-level mitigation appears to be available.

The only effective workaround identified is to **fully disable external boot support** (for example, _USB Storage Boot_) in the UEFI/BIOS configuration.

If external booting is required for legitimate administrative purposes, it must be temporarily enabled by an administrator and disabled again afterward.

### Disclosure Timeline

* **Initial report to HP:** December 2025
* **Additional reproduction details provided (device / BIOS):** 22/12/2025
* **CVE Status:** Pending assignment

### Discoverer

Beatriz Fresno Naumova (beafn28)

### Notes

This behavior may stem from an implicit trust relationship between Windows Recovery Environment and firmware boot policies. While it may be expected from an OS usability perspective, it can conflict with security assumptions in environments that rely on UEFI administrator authentication as a hard boundary.

### References
