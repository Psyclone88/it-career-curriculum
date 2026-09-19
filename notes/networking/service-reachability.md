# Network Service Reachability

When a user says a network service "isn't working," that describes a symptom. It does not identify which layer is failing.

## Troubleshooting Path

A useful evidence chain is:

    correct host
        ↓
    service state
        ↓
    listening socket
        ↓
    local application test
        ↓
    application logs/configuration
        ↓
    firewall
        ↓
    network path
        ↓
    remote client test

Do not assume success at one layer proves success at the next.

Mental model:

    service state ≠ application health ≠ network reachability

## Service State

Check whether systemd considers a service active:

    systemctl is-active SERVICE

Check whether it is configured to start automatically:

    systemctl is-enabled SERVICE

An active service does not prove that the expected network socket exists or that the application works correctly.

## Listening Sockets

Inspect listening TCP sockets:

    sudo ss -lntp

Useful options:

    -l    listening sockets
    -n    numeric addresses and ports
    -t    TCP sockets
    -p    process information

A listening socket provides stronger evidence than service state alone, but still does not prove successful application behaviour.

The address on which a service listens also matters. A service bound only to loopback may work locally while remaining unavailable from another machine.

## Test Locally

For an HTTP service:

    curl -I http://localhost

A successful connection proves more than `systemctl` alone because an application actually received the request.

HTTP status codes also provide evidence.

For example, INC-0004 initially produced:

    HTTP/1.1 403 Forbidden

That was not the same as "no web server." Apache had received the request and generated an HTTP response.

After the document-root problem was corrected:

    HTTP/1.1 200 OK

## Inspect Application Evidence

Systemd logs are useful:

    sudo journalctl -u SERVICE

But applications may maintain their own logs.

During INC-0004, Apache's error log explained the local HTTP 403 when the systemd journal did not provide the needed application-level detail.

Use the evidence source appropriate to the layer being investigated.

## Firewall

A locally working service may still be blocked from remote clients.

Inspect the active firewalld configuration:

    sudo firewall-cmd --list-all

For HTTP:

    sudo firewall-cmd --query-service=http

Runtime and permanent firewalld configuration are separate.

A useful troubleshooting technique is:

    reproduce failure
        ↓
    make one justified runtime change
        ↓
    repeat the same test
        ↓
    compare before and after

If the result changes in the predicted way, that is useful evidence about the cause.

## Test Remotely

A localhost test does not prove that another machine can reach the service.

If the original problem was reported from another machine, perform the final verification from another machine whenever practical.

INC-0004 was not considered resolved merely because Apache worked on `lab-srv01`. The HTTP request was repeated from `cipher-fedora`.

## Persistence

After fixing the immediate problem, determine whether the required configuration should survive reboot.

Relevant examples include:

- enabling the service
- making firewall configuration permanent
- verifying configuration files
- rebooting when appropriate
- repeating the original client-side test afterward

## Remember

- A symptom is not a diagnosis.
- Test one layer at a time.
- Service state does not prove application health.
- Application health on localhost does not prove remote reachability.
- A listening socket does not prove successful application behaviour.
- HTTP errors can themselves be useful evidence.
- Application-specific logs may contain evidence absent from the systemd journal.
- Runtime firewall state and permanent firewall state are different.
- Controlled before-and-after tests are stronger than guessing.
- Final verification should reproduce the original failure conditions as closely as practical.
