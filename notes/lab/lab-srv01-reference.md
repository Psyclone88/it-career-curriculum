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

## Storage

Following INC-0003:

    Virtual disk:     40 GiB
    Root LV:          25 GiB
    Root filesystem:  XFS, approximately 25 GiB
    VG free capacity: approximately 13 GiB

The remaining VG capacity was deliberately left unallocated for future use.

An offline libvirt snapshot named `pre-inc-0003-storage-change` was created before the storage change.

## Web Service

Following INC-0004:

    Web server:       Apache httpd
    httpd.service:    enabled / active
    HTTP port:        TCP 80
    Firewalld:        http service permitted permanently
    Document root:    /var/www/html
    Test page:        /var/www/html/index.html

Remote HTTP access from `cipher-fedora` was verified successfully after reboot.

Check the live service and firewall state rather than assuming this remains true.
## Important Rule

This file is a convenient reference to the known lab configuration.

The live VM and libvirt configuration are authoritative when troubleshooting.
