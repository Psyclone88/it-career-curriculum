# INC-0004 — Web service unreachable

## Symptom

A user reported that a web service on `lab-srv01` could not be reached.

The ticket did not identify which web-server software was expected or which part of the service path was failing.

## Evidence

- `systemctl --failed` initially reported no failed units.
- `ss -lntp` showed nothing listening on the standard HTTP or HTTPS ports 80 or 443.
- Neither Apache (`httpd`) nor Nginx was installed.
- After Apache was installed, it was initially `inactive` and `disabled`.
- Starting Apache made it `active`, and `ss` confirmed that `httpd` was listening on TCP port 80.
- A local `curl` request returned `403 Forbidden`.
- Apache's error log showed that `/var/www/html/` had no matching `index.html` and directory indexing was forbidden.
- Creating a minimal `index.html` changed the local HTTP result to `200 OK`.
- A request from `cipher-fedora` still could not connect to TCP port 80.
- Firewalld was active, and the active `FedoraServer` zone did not allow the `http` service.
- Adding `http` to the runtime firewall configuration immediately changed the remote test from connection failure to `200 OK`.
- Runtime and permanent firewalld configuration were checked separately.
- Apache was active but not initially enabled for automatic startup.

## Diagnosis

The vague report that the web service was unreachable involved multiple missing layers rather than one single fault.

Apache was not initially installed. After installation and startup, the application could not successfully serve the document root because no index page existed. After fixing the application-level problem, remote access was still blocked because firewalld did not permit HTTP traffic.

Apache also needed to be enabled if the service was expected to survive a reboot.

The investigation demonstrated that service state, application health, and network reachability are separate things.

## Resolution

- Installed Apache after inspecting the proposed DNF transaction.
- Started `httpd` and verified that it was listening on TCP port 80.
- Investigated the local `403 Forbidden` response using Apache's error log.
- Created a minimal `/var/www/html/index.html`.
- Verified a local HTTP response of `200 OK`.
- Tested the service remotely from `cipher-fedora`.
- Identified firewalld as the remote-access blocker.
- Added the `http` service to the firewall first as a runtime test.
- Repeated the remote client test and confirmed it changed to `200 OK`.
- Added the HTTP firewall rule permanently.
- Enabled `httpd` for automatic startup.

## Verification

After reboot:

- `httpd` was `active`.
- `httpd` was `enabled`.
- Firewalld still permitted the `http` service.
- A remote request from `cipher-fedora` returned `HTTP/1.1 200 OK`.

The original user-visible symptom was therefore tested successfully after the persistent configuration had survived a reboot.

## Remember

- A vague ticket describes a symptom, not necessarily the faulty component.
- `active` does not mean an application is healthy or remotely reachable.
- A listening socket proves a process is listening; it does not prove the application works correctly.
- An HTTP `403` proves that an HTTP server received the request and generated a response.
- Application-specific logs may explain failures that are not obvious from the systemd journal.
- A successful localhost test does not prove remote reachability.
- Firewalld runtime and permanent configuration are separate.
- A controlled before-and-after test can provide strong evidence that a change addresses the actual fault.
- One ticket can contain multiple problems.
- Final verification should reproduce the original user's test as closely as practical.
- Service state ≠ application health ≠ network reachability.
