---
title: Fortigate
description: 
published: true
date: 2024-04-24T15:25:06.907Z
tags: 
editor: markdown
dateCreated: 2024-04-24T11:30:44.227Z
---

# Installing an preparing a Fortigate Firewall

We will need a Fortigate ova, type qcow2. Also it assumes you have a running Proxmox environment and the Fortinet ova. 

## In Proxmox VE

You will create a new Virtual Machine with the following requirements:

- OS: Click in Do not use any media.

- System: Don't change anything.

- Disk: Delete the Disk. To delete it, you will click in the cabin icon, that is at the right of scsi0.

- CPU: 1 Socket 1 Core.

- Memory: 2048MiB

- Network: At your election.


## In Proxmox via shell

You will write the following command:
```bash
qm importdisk (ID machine) (path of q.cow) local-lvm
```

Once you have it done, we will be back at Proxmox VE.

## Changing boot order, bus of disk and starting.

We will go to hardware, click in Unused Disk 0 and click Edit. We want to change the Bus/Device, to VirtIO Block.

Also we wanna change the boot order, for that we will go to Options > Boot Order. Then select our disk, enable and change it to the first one.

## Inside the Fortigate

Once we have done everything, we can start the machine. We need to login, the credentials are: **admin**/(nothing), the next thing to do is change the password. 

### Setting up an IP.

We need to do the following commands. 

This is for start configure the interfaces.
```bash
config system interface
```

Select the first port.
```bash
edit port1
```

Set up a static IP.
```bash
set mode static
```

Introduce the IP.
```bash
set ip (IP) (NetMask)
```

Go back.
```bash
next
```
Commit the changes.
```bash
end
```

### Setting Up the gateway
Follow the commands.

Start configure a router.
```bash
config router static
```

We select one to edit.
```bash
edit 1
```

Set device to edit.
```bash
set device port1
```

Set the gateway.
```bash
set gateway (IP)
```

Getting out and commit the changes.
```bash
next
end
```

### Connect to the DNS
Follow the commands.

Configure the dns.
```bash
config system dns
```
Setting up the primary dns.
```bash
set primary (IP) [Lazarski: 192.168.8.22]
```

Setting up the secondary dns.
```bash
set secondary (IP) [Google: 8.8.8.8]
```
Getting out and commit the changes.
```bash
next
end
```


## Enter and configure GUI.
To enter, we introduce the IP of the Firewall. We need to login with the credentials of **admin**/(Password we introduce and the beggining). We need to enter in Evaluation license, and we need to introduce our account. This account is create in: https://support.fortinet.com

The system will reboot and in the next login, we can start with the FortiGate Setup. 
