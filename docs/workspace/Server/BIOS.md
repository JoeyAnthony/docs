---
tags:
  - Powersaving - PC
---

# BIOS powersaving tricks

## CPU and Package C-states
Typically go from C1 - C10 where C10 is a low idle power mode.

Higher package powerstates can be hard to reach since it depends on whether the motherboard and other hardware components support it in their drivers.

## ASPM

[Source](https://www.dell.com/support/manuals/nl-nl/optiplex-5055-ryzen-cpu/opti5055mt_om_pub/active-state-power-management?guid=guid-c3d9948b-0f0e-4217-8a04-d794e1712d55&lang=en-us)

ASPM is the power management capability of hardware to effectively reduce usage of power by placing the PCI Express(PCIe) based serial link devices to low-power state when not in use. ASPM is controlled by BIOS or the power management component of the operating system in two configurations.

### PCIE ASPM support
L0 – All PCI Express transactions and other operations are enabled.

L0s – A low resume latency, energy saving “standby” state.

L1 – Higher latency, lower power “standby” state (optional).

L0sL1 - Activate both L0s and L1 support.
