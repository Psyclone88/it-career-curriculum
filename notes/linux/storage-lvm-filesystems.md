# Linux Storage — LVM and Filesystems

Linux storage has multiple layers. Do not assume that disk size, logical-volume size, and filesystem size are the same thing.

## Storage Layers

Mental model:

    disk
      ↓
    partition
      ↓
    physical volume (PV)
      ↓
    volume group (VG)
      ↓
    logical volume (LV)
      ↓
    filesystem
      ↓
    mount point

Simplified LVM model:

    PV supplies storage → VG pools storage → LV allocates storage → filesystem organizes files

## Inspecting Storage

Show mounted filesystem usage:

    df -h

Show filesystem types as well:

    df -Th

Show block devices and their relationships:

    lsblk

Inspect LVM physical volumes:

    sudo pvs

Inspect LVM volume groups:

    sudo vgs

Inspect LVM logical volumes:

    sudo lvs

These commands answer different questions. Do not substitute one for another.

## Filesystem Space vs VG Space

`df` reports space available inside a mounted filesystem.

`vgs` can show capacity that exists inside an LVM volume group but has not yet been allocated to a logical volume.

Therefore:

    free VG space ≠ free filesystem space

INC-0003 demonstrated this on `lab-srv01`.

The VM had a 40 GiB virtual disk and approximately 23 GiB free inside its volume group, while the root logical volume and root filesystem were only approximately 15 GiB.

The capacity was not missing. It existed at a different storage layer.

## Logical Volumes and Filesystems

A logical volume provides block storage.

A filesystem such as XFS or ext4 organizes files and directories on that block storage.

Increasing the size of an LV does not conceptually mean that the filesystem stored on it has also grown.

During INC-0003, the root LV was expanded from 15 GiB to 25 GiB:

    sudo lvextend -L +10G /dev/fedora_lab-srv01/root

Afterward, `lvs` showed the larger LV while `df` still showed the old filesystem size.

The XFS filesystem was then expanded:

    sudo xfs_growfs /

Only then did `df` report the additional usable filesystem capacity.

## Safe Change Pattern

Before changing storage:

1. Identify the actual disk and storage layers.
2. Confirm the filesystem type.
3. Confirm where free capacity exists.
4. Understand exactly which layer the proposed command changes.
5. Have an appropriate recovery plan.
6. Make the smallest justified change.
7. Verify the affected layers afterward.
8. Verify persistence where appropriate.

Storage commands can be destructive. Never copy an LV, filesystem, partition, or device command blindly from notes.

## Remember

- Disk size is not necessarily filesystem size.
- PV = Physical Volume.
- VG = Volume Group.
- LV = Logical Volume.
- A VG pools storage supplied by one or more PVs.
- An LV receives allocated capacity from a VG.
- A filesystem lives on block storage such as an LV.
- `df` describes mounted filesystems.
- `lsblk` describes block-device relationships.
- `pvs`, `vgs`, and `lvs` inspect different LVM layers.
- Unallocated VG capacity does not appear as filesystem free space.
- Growing an LV and growing its filesystem are separate concepts.
- Inspect first, change second, verify afterward.
