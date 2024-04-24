---
title: Palo Alto on Proxmox VM
description: Guide how to install and configure Pal alto on Proxmox VM
published: true
date: 2024-03-05T16:42:43.512Z
tags: paloalto, firewall, proxmox, vm
editor: markdown
dateCreated: 2024-02-29T18:01:55.656Z
---

To install the Palo Alto PA-VM-ESX-10.2.6.ova firewall on a Proxmox VM, follow these steps carefully. This guide assumes you have a running Proxmox environment, have downloaded the Palo Alto PA-VM-ESX-10.2.6.ova file, and are prepared to configure necessary network settings, including setting up additional network interfaces.

### Step 1: Prepare the Environment

Ensure your Proxmox system is up to date to prevent compatibility issues and to ensure security patches are applied:

```bash
apt update && apt upgrade -y
```

### Step 2: Install Required Tools

The `unzip` utility is required to extract the OVA file, and `qm` is Proxmox's command-line tool for managing VMs:

```bash
apt install unzip -y
```

### Step 3: Extract the OVA File

Navigate to the directory where the PA-VM-ESX-10.2.6.ova file is located. Use the `tar` command to extract its contents:

```bash
tar -xvf PA-VM-ESX-10.2.6.ova
```

This extracts the OVF file and one or more VMDK files.

### Step 4: Create a New VM

Set up a new VM in Proxmox for the Palo Alto firewall. This prepares the environment but doesn't start the VM:

```bash
qm create 100 --name "PaloAlto-VM" --memory 12288 --cores 4 --net0 virtio,bridge=vmbr0 --net1 virtio,bridge=vmbr1
```
```bash
qm create 105 --name "PA-VM" --memory 6656 --cores 4 --scsihw virtio-scsi-pci --hotplug disk,network,usb,cpu --serial0 socket --boot order='virtio0' --net0 virtio,bridge=vmbr0 --net1 virtio,bridge=vmbr0 --net2 virtio,bridge=vmbr1
```
Ensure the VM ID (`100` or `105` in this example) is unique.

### Step 5: Import the Disk

Import the system disk (VMDK file) to the VM you've just created:

```bash
qm importdisk 100 PA-VM-ESX-10.2.6-disk1.vmdk local-lvm
```

Choose the appropriate storage ID for your environment in place of `local-lvm`.

### Step 6: Attach the Disk to the VM

The next step is attaching the imported disk to the VM:

```bash
qm set 100 --scsihw virtio-scsi-pci --scsi0 local-lvm:vm-100-disk-0
```

### Step 7: Configure VM Options

Configure the VM to boot from the disk. Additionally, set up serial console access:

```bash
qm set 100 --boot c --bootdisk scsi0
qm set 100 --serial0 socket --vga serial0
```

### Step 8: Start the VM

You can now start the VM:

```bash
qm start 100
```

Wait a few minutes for the VM to boot up fully.

### Step 9: Access the Palo Alto VM

Connect to the VM's console via the Proxmox GUI or a serial console connection to begin the initial setup according to Palo Alto's guidelines.

You need to try to login with username **admin** and password **admin**, three times. Once you did it, you try it again and it will ask for a new password, this password should be:
- 8 Characters long.
- 1 Upper letter.
- 1 Lower letter.
- 1 Special symbol.

### Step 10: Complete Setup

Follow the instructions provided on the screen to finish setting up the Palo Alto VM, including the configuration of interfaces, zones, and policies to meet your specific network requirements.

### Network Configuration and PCIe Network Interface Setup

After installing the Palo Alto VM, ensuring that your Proxmox server is correctly integrated into your network is crucial. This might involve configuring additional network interfaces, such as a newly installed PCIe network interface.

### Identify and Prepare the New Network Interface and getting access to the panel.

Once we are in the VM to do a couples of commands to start getting access to the panel.

1. **Enter in configure mode**: We need to enter in the configure mode, to start editing the VM:

   ```bash
   configure
   ```

2. **Make the IP static**: Next step is make the IP static:

   ```bash
   set deviceconfig system type static
   ```

3. **Set a IP**: Next step is define our IP, netmask and gateway.

    ```bash
   set deviceconfig system ip-address your_ip_firewall netmask your_mask default-gateway your_gateway_ip
   ```

4. **Commit the changes**: Now we need to commit all the changes.

   ```bash
   commit
   ```
   
Now to get access to the panel, we need to search the firewall ip, in any browser. The credentials will be the same as you put on in "Acess the Palo Alto VM".

### Additional Considerations

- **System Updates**: Regularly check for and apply updates to the Palo Alto VM and Proxmox to ensure security and stability.
- **Security**: Configure firewalls and access controls to secure your Palo Alto VM and Proxmox environment.
- **Backup**: Regularly back up your VM and configuration to prevent data loss.

This comprehensive guide provides a foundation for installing and configuring a Palo Alto firewall VM within a Proxmox virtual environment, including network configuration considerations vital for successful integration into your infrastructure.
