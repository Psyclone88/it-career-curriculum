# Routing and Default Gateways

## Local vs Remote Networks

A computer uses its IP address and prefix length to determine whether a destination is on the local network or on a different network.

Example workstation:

    IP address: 192.168.2.119/24
    Network:    192.168.2.0/24

With this configuration:

    192.168.2.50 → local network
    192.168.3.50 → remote network
    8.8.8.8      → remote network

For a destination on the local network, the computer can communicate with the destination directly at the local network layer.

For a destination on a different network, the computer normally sends the traffic toward a router.

Mental model:

    Same network      → communicate locally
    Different network → router needed

## Default Gateway

The default gateway is the router a computer normally sends traffic to when it does not have a more specific route for the destination.

On my home network:

    Fedora workstation: 192.168.2.119/24
    Default gateway:     192.168.2.1

In the career lab:

    lab-srv01:           10.20.0.106/24
    Default gateway:     10.20.0.1

The gateway itself must normally be reachable on the machine's local network.

## Inspecting Routes

Useful command:

    ip route

Example from a typical system:

    default via 192.168.2.1 dev wlp5s0
    192.168.2.0/24 dev wlp5s0

Interpretation:

    192.168.2.0/24
        → traffic for the local network uses wlp5s0

    default via 192.168.2.1
        → traffic without a more specific route is sent toward 192.168.2.1

`default` does not mean that all traffic goes through the gateway.

Traffic for a directly connected local network can use its more specific local route.

Mental model:

    Destination
        ↓
    Check routing table
        ↓
    Matching local/specific route? → use it
        ↓ no
    Use default route

## Troubleshooting a Gateway

If a machine cannot reach the Internet, checking the gateway is an important early test.

Example:

    ping 10.20.0.1

If `lab-srv01` cannot reach `10.20.0.1`, investigate the local network path before assuming the Internet or DNS is the problem.

If the gateway works, continue outward:

    ping 1.1.1.1

Then test name resolution:

    ping example.com

## Remember

- A route tells the system where to send traffic.
- Local and more-specific routes take precedence over the default route.
- The default gateway handles traffic when there is no more-specific matching route.
- `ip route` shows the routing decisions configured on the system.
- Test from the nearest dependency outward: local configuration → gateway → external IP → DNS/name.
