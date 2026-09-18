# Addressing and Network Interfaces

## Network Interface

A network interface is how a computer communicates with a network. It can represent physical hardware such as Wi-Fi or Ethernet, or it can be virtual.

On my Fedora workstation:

    wlp5s0 → Wi-Fi network interface

Mental model:

    Interface = HOW the computer connects
    IP address = WHERE that interface is addressed on the IP network

## Useful command

    ip -brief address

This displays network interfaces, their state, and their assigned addresses.

Example:

    wlp5s0    UP    192.168.2.119/24

This tells me:

- `wlp5s0` is the interface.
- `UP` means the interface is up.
- `192.168.2.119` is its IPv4 address.
- `/24` is the network prefix.

## Loopback

The loopback interface allows a computer to communicate with itself through its own networking stack.

On Linux:

    lo          → loopback interface
    127.0.0.1   → loopback IPv4 address
    localhost   → common hostname for the local machine

Mental shortcut:

    127.0.0.1 = this computer

A service listening only on `127.0.0.1` is normally accessible from the same machine but not directly from another computer on the network.

Example troubleshooting clue:

    Web application works locally but other computers cannot connect.

One hypothesis is that the application is listening only on the loopback interface rather than an externally reachable interface.

## Host Address vs Network

My Fedora workstation:

    IP address: 192.168.2.119/24
    Network:    192.168.2.0/24

These are not the same thing.

`192.168.2.119` identifies my Fedora interface on this network.

`192.168.2.0/24` describes the network containing addresses in the `192.168.2.x` range.

For this `/24` network:

    192.168.2.50    → local
    192.168.2.119   → local
    192.168.2.200   → local
    192.168.3.50    → different network
    8.8.8.8         → different network

## Remember

- Interface = how the computer connects.
- IP address = where an interface is addressed on an IP network.
- `lo` is the loopback interface.
- `127.0.0.1` means localhost / this machine.
- `/24` is the prefix length; `192.168.2.119/24` is CIDR notation combining the IP address with its network prefix length.

