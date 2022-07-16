# Introduction

This document will cover all the issues that I have encountered while setting up my home lab using proxmox as the hypervisor.


## Tasks, issues and their solutions

**TASK:** GPU Passthrough  
**Issues:**  
1. **Scenario:** I have a VM with the following configuration  
   1. Single GPU plugged in to the system
   2. VM created with windows 10/11
   3. PCI card added to the VM via the UI of proxmox with the following enabled  
      a. All Functions, Primary GPU, ROM-Bar and PCI-Express
   4. Following added to the kernel boot parameter using `/etc/defaults/grub`'s `GRUB_CMDLINE_LINUX_DEFAULT` parameter. `amd_iommu=on video=simplefb:off` added to this config value.
   5. For further configuration, please refer [here](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#qm_pci_passthrough), [here](https://pve.proxmox.com/wiki/Pci_passthrough) and [here](https://forum.proxmox.com/threads/gpu-passthrough-issues-after-upgrade-to-7-2.109051/) for more details.  
    
   **Problem:** The GPU wouldn't pass through as the linux kernel and UEFI would still hold the GPU, despite adding `video=simplefb:off`.  
   **Solution:** Before starting the VM, I had to add a [hookscript](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#_hookscripts) which would release the GPU before the guest could start.
