# INC-0003 — Root filesystem smaller than virtual disk

## Symptom

`lab-srv01` was reported as running out of disk space despite having a 40 GiB virtual disk.

Investigation showed that the filesystem was not actually close to full. The real issue was a mismatch between the virtual disk capacity and the capacity allocated to the root filesystem.

## Evidence

- The VM had a 40 GiB virtual disk.
- `lsblk` showed the main LVM partition was approximately 38 GiB.
- `df -Th /` showed the root XFS filesystem was only approximately 15 GiB.
- `lvs` showed the root logical volume was 15 GiB.
- `vgs` showed approximately 23 GiB remained free inside the volume group.
- The supposedly missing disk capacity therefore existed inside the LVM volume group but had not been allocated to the root logical volume.
- An offline libvirt snapshot named `pre-inc-0003-storage-change` was created before modifying the storage layout.

## Diagnosis

The server was not actually running out of filesystem space.

The 40 GiB virtual disk existed and Linux could see it, but only 15 GiB had been allocated to the root logical volume and its XFS filesystem. Approximately 23 GiB remained available inside the LVM volume group.

This demonstrated that virtual disk size, LVM allocation, and filesystem size are separate layers.

## Resolution

- Confirmed the available capacity at the disk, partition, PV, VG, LV, and filesystem layers.
- Created a recovery snapshot before making the storage change.
- Extended the root logical volume by 10 GiB, from 15 GiB to 25 GiB.
- Observed that increasing the LV did not automatically change the size reported by `df`.
- Grew the XFS filesystem to use the additional LV capacity.
- Deliberately left approximately 13 GiB free in the volume group for future use.

## Verification

After the change and reboot:

- The root logical volume was 25 GiB.
- The root XFS filesystem was approximately 25 GiB.
- Approximately 13 GiB remained free in the volume group.
- `systemctl --failed` reported no failed units.
- The storage changes persisted across reboot.

## Remember

- A ticket description is a report to investigate, not a diagnosis to accept.
- Disk capacity, partition capacity, LVM capacity, and filesystem capacity are different things.
- LVM follows the model: PV supplies storage → VG pools storage → LV allocates storage → filesystem organizes files.
- `df` reports filesystem capacity; it does not show unused capacity inside an LVM volume group.
- Extending an LV and growing its filesystem are conceptually separate operations.
- Inspect every storage layer before making changes.
- Consequential storage changes should have a recovery plan and should be verified afterward.
