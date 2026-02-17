# Orion – Minimal KVM Hypervisor

**Orion** is a lightweight hypervisor project built to understand and experiment with virtualization infrastructure using open-source components.

The goal is to **start with a clean, single-host hypervisor** and gradually evolve it toward a **distributed virtualization platform** similar in architecture to Proxmox, oVirt, or OpenStack.

This project focuses on learning fundamentals first, then layering advanced features in a structured way.

---

## Project Philosophy

Instead of starting with a complex cluster, Orion follows this progression:

1. Build a reliable **single-node hypervisor**
2. Understand storage, networking, and VM lifecycle deeply
3. Introduce shared storage and advanced networking
4. Expand toward a **multi-node distributed system**

This mirrors how real infrastructure is designed and deployed.

---

## Project Goals

* Build a minimal hypervisor from scratch
* Understand KVM, QEMU, and libvirt internals
* Implement local storage using Btrfs
* Learn VM lifecycle and automation workflows
* Add bridge networking and Open vSwitch
* Introduce shared storage and clustering concepts later
* Document each step for reproducibility and learning

---

## Architecture Overview (Current Phase)

Hardware
→ Proxmox (Test Bench / Nested Virtualization)
→ Debian Hypervisor (Orion – Single Node)
→ Guest Virtual Machines

Future Architecture:

Multiple Hypervisor Nodes
→ Shared Storage
→ Overlay Networking
→ Distributed VM Management

---

## Technology Stack

### Hypervisor

* KVM
* QEMU
* libvirt
* virsh

### Storage (Phase 1 – Single Node)

* Btrfs (local storage)

### Storage (Future)

* NFS
* Distributed storage (Ceph – experimental phase)

### Networking (Phase 1)

* Linux bridge

### Networking (Future)

* Open vSwitch
* OVN

### Management Tools

* virt-install
* virsh
* cloud-init (planned)

---

## Base Operating System

* Debian 12 (Minimal Installation)

Reasons:

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

Persist mount in `/etc/fstab`.

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
--disk pool=vmstore,size=10,bus=virtio \
--network network=default,model=virtio \
--os-variant rocky9 \
--graphics vnc \
--boot cdrom,hd \
--cdrom /path/to.iso \
--noautoconsole
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

## Current Status (Phase 1 – Single Host)

* [x] Minimal OS installed
* [x] KVM and libvirt configured
* [x] Nested virtualization working
* [x] Btrfs storage configured
* [x] Default network enabled
* [x] VM creation workflow validated
* [ ] Bridge networking
* [ ] VM templates and cloud-init
* [ ] NFS storage
* [ ] Open vSwitch

---

## Roadmap

### Phase 1 – Single Node (Current Focus)

* Reliable hypervisor setup
* Storage and networking fundamentals
* VM templates and automation

### Phase 2 – Advanced Features

* Bridge networking
* Open vSwitch
* Performance tuning

### Phase 3 – Distributed System

* Shared storage
* Multi-node orchestration
* Overlay networking (OVN)
* High availability concepts

---

## Learning Objectives

This project focuses on understanding:

* Linux virtualization internals
* Storage backends and performance
* Virtual networking concepts
* Hypervisor architecture
* Real-world troubleshooting workflows
* Gradual evolution toward distributed infrastructure

---

## License

MIT License

---

## Author

Manoj Kumar
Orion Hypervisor Project
