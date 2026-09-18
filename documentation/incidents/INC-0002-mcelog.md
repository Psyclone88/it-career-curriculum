# INC-0002 — mcelog service failure

## Symptom

`systemctl --failed` reported `mcelog.service` in a failed state after boot.

## Evidence

- `mcelog.service` exited with status 1.
- Journal output reported: `AMD Processor family 23: mcelog does not support this processor`.
- `lscpu` identified the guest CPU as AMD family 23 and confirmed the system is a KVM virtual machine.
- `mcelog` was installed automatically by the Fedora Server installation as part of a package group.
- No installed package reported `mcelog` as a requirement.
- The kernel provides the `edac_mce_amd` module, although it was not loaded.
- `/dev/mcelog` existed, allowing the systemd conditions for `mcelog.service` to be satisfied.

## Diagnosis

`mcelog` was incompatible with the AMD family 23 CPU presented to the Fedora guest by KVM. The service was enabled and attempted to start because its systemd conditions were satisfied, but `mcelog` rejected the CPU and exited with an error.

This was a service/software compatibility issue rather than evidence of a CPU hardware failure.

## Resolution

- Installed and tested `rasdaemon` as the replacement RAS monitoring daemon.
- Verified `rasdaemon.service` could run successfully before enabling it.
- Disabled `mcelog.service` to prevent future automatic startup attempts.
- Cleared the existing `mcelog.service` failed state.
- Enabled `rasdaemon.service` for automatic startup.
- Rebooted the VM to test that the configuration persisted.

## Verification

After reboot:

- `rasdaemon` was `enabled` and `active`.
- `mcelog` was `disabled` and `inactive`.
- `systemctl --failed` reported `0 loaded units listed`.
- SSH access to the server was restored successfully after reboot.

## Remember

- `active` describes whether a service is running now.
- `enabled` describes whether a service is configured to start automatically.
- A failed service should be investigated before disabling or removing it.
- Gather evidence before making changes, then verify the result afterward.
- In a VM, hardware-related services may behave differently because the guest sees hardware presented by the hypervisor.
