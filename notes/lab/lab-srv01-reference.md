# lab-srv01 Reference

`lab-srv01` is the first Fedora Server VM in the I.T Career Curriculum lab.

This document records its current known configuration.

## Identity

    Hostname:       lab-srv01
    OS:             Fedora Server 44
    Architecture:   x86_64
    Virtualization: KVM/libvirt

## Virtual Hardware

    vCPUs:          2
    Memory:         4096 MiB
    Virtual disk:   40 GiB
    Disk image:     /var/lib/libvirt/images/lab-srv01.qcow2
    Machine type:   Q35
    Firmware:       BIOS

## Network

    libvirt network: career-lab
    IPv4 address:    10.20.0.106/24
    Default gateway: 10.20.0.1
    DNS server:      10.20.0.1
    MAC address:     52:54:00:0c:ea:49

SSH from the Fedora workstation:

    ssh cipher@10.20.0.106

The address was originally assigned through DHCP.

## Current Service State

Following INC-0002:

    mcelog.service   → disabled / inactive
    rasdaemon.service → enabled / active

The intended state was verified after reboot.

Check current state rather than assuming this remains true:

    systemctl is-enabled mcelog rasdaemon
    systemctl is-active mcelog rasdaemon
    systemctl --failed

## Storage Observation

The VM has a 40 GiB virtual disk, but the root filesystem was observed to use only about 15 GiB.

This remains something to investigate later as part of Linux storage/LVM training.

## Important Rule

This file is a convenient reference to the known lab configuration.

The live VM and libvirt configuration are authoritative when troubleshooting.
