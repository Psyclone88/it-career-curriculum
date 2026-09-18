# DHCP and DNS

DHCP and DNS solve different networking problems.

## DHCP — Getting Network Configuration

DHCP stands for Dynamic Host Configuration Protocol.

Its job is to automatically provide a device with network configuration such as:

- IP address
- subnet/prefix information
- default gateway
- DNS server addresses

Mental model:

    DHCP = "What network settings should I use?"

Example:

A new VM boots and receives:

    IP address:      10.20.0.106/24
    Default gateway: 10.20.0.1
    DNS server:      10.20.0.1

DHCP helped provide that configuration.

## DNS — Resolving Names

DNS stands for Domain Name System.

Its job is to translate names into IP addresses.

Mental model:

    DNS = "What IP address belongs to this name?"

Example:

    example.com → IP address

If this succeeds:

    ping 1.1.1.1

but this fails because the name cannot be resolved:

    ping example.com

then basic IP connectivity may be working while DNS resolution has a problem.

## How DHCP and DNS Relate

DHCP can provide the address of a DNS server as part of the network configuration it gives a device.

For example:

    DHCP provides:
        IP address      → 10.20.0.106/24
        Default gateway → 10.20.0.1
        DNS server      → 10.20.0.1

This does NOT mean DHCP performs DNS resolution.

DHCP supplied the DNS configuration.
DNS performs the name lookup.

Mental model:

    DHCP → gives me my network settings
    DNS  → resolves names to addresses

## Troubleshooting

Do not immediately blame DNS just because the Internet appears broken.

Test the network in stages:

    1. Is the interface up?
    2. Does it have an IP address?
    3. Can it reach its default gateway?
    4. Can it reach an external IP address?
    5. Can it resolve and reach a hostname?

Example:

    ping 10.20.0.1
    ping 1.1.1.1
    ping example.com

If the gateway works and `1.1.1.1` works, but `example.com` fails to resolve, 
DNS becomes a strong area to investigate.

## Remember

- DHCP provides network configuration.
- DNS resolves names.
- DHCP can tell a client which DNS server to use.
- Successful IP connectivity does not prove DNS is working.
- Test dependencies in order instead of guessing the cause.


