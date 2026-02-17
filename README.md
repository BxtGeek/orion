# Orion – Minimal KVM Hypervisor Lab

Orion is a lightweight hypervisor lab built to understand and experiment with virtualization infrastructure using open-source components.
The goal of this project is to build a small, production-style virtualization stack step by step, similar in architecture to platforms like Proxmox, oVirt, and OpenStack.

---

## Project Goals

* Build a minimal hypervisor from scratch
* Understand KVM, QEMU, and libvirt internals
* Implement storage using Btrfs and NFS
* Build software-defined networking using Open vSwitch and OVN
* Document each step for reproducibility and learning

---

## Architecture Overview

Hardware
→ Proxmox (Test Bench / Nested Virtualization)
→ Debian Hypervisor (Orion)
→ Guest Virtual Machines

---

## Technology Stack

**Hypervisor**

* KVM
* QEMU
* libvirt
* virsh

**Storage**

* Btrfs (local storage)
* NFS (shared storage – planned)

**Networking**

* Linux bridge (initial)
* Open vSwitch (planned)
* OVN (planned)

**Management Tools**

* virt-install
* virsh
* cloud-init (planned)

---

## Base Operating System

* Debian 12 (Minimal Installation)

Reason:

* Stable kernel
* Excellent libvirt and KVM support
* Minimal overhead
* Similar base to Proxmox

---

## Installation Steps

### 1. Install Base System

Minimal Debian installation with:

* SSH server
* Standard system utilities

Update system:

```bash
apt update
apt upgrade
```

---

### 2. Install Hypervisor Components

```bash
apt install qemu-kvm libvirt-daemon-system libvirt-clients virtinst bridge-utils
```

Verify:

```bash
virsh list --all
```

---

### 3. Enable Nested Virtualization (if running inside Proxmox)

On Proxmox host:

```bash
echo "options kvm_intel nested=Y" > /etc/modprobe.d/kvm-intel.conf
reboot
```

Set VM CPU type to:

```
host
```

Verify inside VM:

```bash
ls -l /dev/kvm
```

---

### 4. Configure Storage (Btrfs)

Format disk:

```bash
mkfs.btrfs /dev/sdb
```

Mount:

```bash
mkdir /vmstore
mount /dev/sdb /vmstore
```

Add to `/etc/fstab`.

Create storage pool:

```bash
virsh pool-define-as vmstore dir - - - - /vmstore
virsh pool-start vmstore
virsh pool-autostart vmstore
```

---

### 5. Enable Default Network

```bash
virsh net-start default
virsh net-autostart default
```

---

### 6. Create First VM

```bash
virt-install \
--name testvm \
--ram 1024 \
--vcpus 1 \
--disk pool=vmstore,size=10 \
--os-variant generic \
--network network=default \
--graphics none \
--console pty,target_type=serial \
--cdrom /path/to.iso
```

---

## Directory Layout

Recommended structure:

```
/vmstore
   ├── iso/
   ├── images/
   ├── templates/
```

---

## Current Status

* [x] Minimal OS installed
* [x] KVM and libvirt configured
* [x] Nested virtualization working
* [x] Btrfs storage configured
* [x] Default network enabled
* [ ] Bridge networking
* [ ] NFS storage
* [ ] Open vSwitch
* [ ] OVN networking
* [ ] Automation and templates

---

## Future Enhancements

* Cloud-init templates
* Automated VM provisioning
* Multi-node clustering
* Monitoring and metrics
* Infrastructure as Code (Terraform / Ansible)

---

## Learning Objectives

This project focuses on understanding:

* Linux virtualization internals
* Storage backends and performance
* Software-defined networking
* Hypervisor architecture
* Troubleshooting real-world setups

---

## License

MIT License

---

## Author

Manoj Kumar
Orion Hypervisor Lab
