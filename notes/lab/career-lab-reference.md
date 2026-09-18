# Career Lab Reference

This file records the current network configuration of my I.T Career Curriculum virtual lab.

It is an environment reference, not a general networking tutorial.

## Network Overview

My Fedora workstation hosts the lab using libvirt/KVM.

There are two separate libvirt virtual networks:

    default
    career-lab

The standard libvirt `default` network has been left intact.

## Default libvirt Network

    Network name: default
    Mode:         NAT
    Bridge:       virbr0
    Network:      192.168.122.0/24
    Gateway:      192.168.122.1

This is libvirt's standard virtual network and is not the dedicated curriculum network.

## Career Lab Network

    Network name: career-lab
    Mode:         NAT
    Bridge:       virbr-career
    Network:      10.20.0.0/24
    Gateway:      10.20.0.1
    DHCP range:   10.20.0.100 - 10.20.0.200

The network is configured to:

    start automatically
    provide DHCP to lab VMs
    use NAT for connectivity outside the virtual network

Mental picture:

    Internet
       |
    Home router
       |
    Fedora workstation
       |
    libvirt NAT
       |
    career-lab — 10.20.0.0/24
       |
    Lab VMs

## Inspecting the Lab Network

List libvirt networks:

    sudo virsh net-list --all

Inspect the `career-lab` network:

    sudo virsh net-info career-lab

View its XML configuration:

    sudo virsh net-dumpxml career-lab

Inspect the bridge from Fedora:

    ip -brief address show virbr-career

These commands can be used to verify that this reference still matches the actual system.

## Important Addresses

    Fedora home interface:  192.168.2.119/24
    Home gateway:           192.168.2.1

    career-lab network:     10.20.0.0/24
    career-lab gateway:     10.20.0.1

    lab-srv01:              10.20.0.106/24

The home network and `career-lab` are separate networks.

## Remember

- `career-lab` is the dedicated curriculum virtual network.
- It uses the `10.20.0.0/24` network.
- `10.20.0.1` is its gateway.
- `virbr-career` is its host-side virtual bridge.
- Lab VMs receive addresses through DHCP.
- NAT allows lab systems to reach networks outside `career-lab`.
- Do not assume this document is permanently correct; verify the live configuration when accuracy matters.
