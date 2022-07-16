# Introduction

This document will cover all the issues that I have encountered while setting up my home lab using proxmox as the hypervisor.


## Tasks, issues and their solutions

**TASK:** GPU Passthrough  
**Issues:**  
1. **Scenario:** Trying to install proxmox for the first time on a Ryzen 3700x with Nvidia GT 730
   **Problem:** The proxmox ui installation UI doesn't load when installing for the first time.  
   **Solution:** Refer to this [link](https://robertoviola.cloud/2020/04/16/proxmox-no-screen-during-installation/)  
   It basically asks to perform the following steps
   1. Run the intallation in debug mode from the loader screen.
   2. `cd /etc/X11`
   3. `Xorg -configure` - Pay attention to where the file is created. If it is created at the root(/) then run `mv mv /xorg.conf.new xorg.conf`
   4. Open in the newly copied `xorg.conf` file, and find `Section "Screen"` and then for every `SubSection "Display"` add `Modes     "1024x768"`. The resolution on the 'modes' line should be based on the resolution supported by your display.
2. **Scenario:** I have a VM with the following configuration  
   1. Single GPU plugged in to the system
   2. VM created with windows 10/11
   3. PCI card added to the VM via the UI of proxmox with the following enabled  
      a. All Functions, Primary GPU, ROM-Bar and PCI-Express
   4. Following added to the kernel boot parameter using `/etc/defaults/grub`'s `GRUB_CMDLINE_LINUX_DEFAULT` parameter. `amd_iommu=on video=simplefb:off` added to this config value.
   5. For further configuration, please refer [here](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#qm_pci_passthrough), [here](https://pve.proxmox.com/wiki/Pci_passthrough) and [here](https://forum.proxmox.com/threads/gpu-passthrough-issues-after-upgrade-to-7-2.109051/) for more details.  
    
   **Problem:** The GPU wouldn't pass through as the linux kernel and UEFI would still hold the GPU, despite adding `video=simplefb:off`.  
   **Solution:** Before starting the VM, I had to add a [hookscript](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#_hookscripts) which would release the GPU before the guest could start.
3. **Scenario:** By default, wireless connectivity is not enabled in proxmox by default reasons are stated [here](https://pve.proxmox.com/wiki/WLAN). Also, cannot use the guests while they are connected to the wireless network only.    
   **Problem:** It is to be made sure that where ever the system is placed, it has wired connectivity. In case of GPU, keyboard, mouse and other passthrough, using a guest as a standalone system is a bit difficult if it needs wired connectivity on the host.  
   **Solution:** As listed out [here](https://pve.proxmox.com/wiki/WLAN), I can either [Bridge Port Using ebtables](https://pve.proxmox.com/wiki/WLAN#Bridge_Port_Using_ebtables) or enable [4 address WDS](https://pve.proxmox.com/wiki/WLAN#4_address_mode_.28WDS.29) either of which don't seem to be really good solution due to the fact that wlan doesn't support bridging.
