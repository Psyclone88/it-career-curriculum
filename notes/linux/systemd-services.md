# systemd Services

`systemd` is the system and service manager used by Fedora.

`systemctl` is the main command used to inspect and control systemd units.

## Service State

Several different properties describe a service. Do not treat them as meaning the same thing.

### Active

`active` describes whether the service is running now.

Example:

    systemctl is-active rasdaemon

Possible result:

    active

Mental model:

    active = running now

### Enabled

`enabled` describes whether the service is configured to start automatically when its associated systemd target is reached, commonly during boot.

Example:

    systemctl is-enabled rasdaemon

Possible result:

    enabled

Mental model:

    enabled = configured to start automatically

A service can therefore be:

    active + enabled
    active + disabled
    inactive + enabled
    inactive + disabled

This is important:

    active ≠ enabled

One describes the current runtime state.
The other describes automatic startup configuration.

## Inspecting Services

Check the detailed status of a service:

    systemctl status SERVICE

Example:

    systemctl status mcelog.service

This can show:

- whether the unit is loaded
- whether it is active, inactive, or failed
- whether it is enabled or disabled
- recent log messages
- the process exit status

Check all currently failed units:

    systemctl --failed

For a compact answer about one service:

    systemctl is-active SERVICE
    systemctl is-enabled SERVICE

Example:

    systemctl is-active rasdaemon
    systemctl is-enabled rasdaemon

## Inspecting the Unit Definition

To see the systemd configuration that applies to a unit:

    systemctl cat SERVICE

Example:

    systemctl cat mcelog.service

This can reveal important details such as:

- `ExecStart=` — command systemd runs
- `EnvironmentFile=` — environment/configuration file used
- `After=` — ordering relationship
- `WantedBy=` — target associated with enabling the unit
- `Condition...=` — conditions that must be satisfied before systemd starts the unit

## Troubleshooting Rule

Do not immediately disable, remove, or reinstall a failed service.

First gather evidence:

    systemctl --failed
        ↓
    systemctl status SERVICE
        ↓
    inspect logs/error messages
        ↓
    systemctl cat SERVICE
        ↓
    understand what the service does
        ↓
    determine why it failed
        ↓
    only then decide what change is appropriate

Mental model:

    Investigate before changing.

## Changing Service State

Start a service now:

    sudo systemctl start SERVICE

Stop a service now:

    sudo systemctl stop SERVICE

These affect the current runtime state but do not necessarily change what happens at the next boot.

Enable automatic startup:

    sudo systemctl enable SERVICE

Disable automatic startup:

    sudo systemctl disable SERVICE

These change startup configuration but do not necessarily start or stop the service immediately.

Mental model:

    start / stop       → what happens now
    enable / disable   → what should happen automatically later

## Failed State

After the cause of a failure has been investigated and dealt with, systemd's recorded failed state can be cleared with:

    sudo systemctl reset-failed SERVICE

Example from INC-0002:

    sudo systemctl reset-failed mcelog.service

`reset-failed` does not repair the underlying problem.

It only clears systemd's recorded failed state.

Always fix or deliberately handle the cause first, then clear the failed state.

## Verification

After making a service change, verify the result instead of assuming the command achieved the intended outcome.

Useful checks:

    systemctl is-active SERVICE
    systemctl is-enabled SERVICE
    systemctl --failed

For changes intended to survive reboot, a reboot can provide stronger verification:

    sudo systemctl reboot

After reconnecting, check the service state again.

Mental model:

    Change → verify → prove persistence when necessary

## Unit Conditions

A systemd unit can contain conditions that determine whether systemd should attempt to start it.

Example:

    ConditionPathExists=/dev/mcelog

This means the path must exist for the condition to succeed.

A condition beginning with `!` reverses the test:

    ConditionPathExists=!/sys/module/edac_mce_amd/initstate

Mental model:

    path     → must exist
    !path    → must NOT exist

Conditions are useful troubleshooting evidence. A service may be installed and enabled but deliberately skipped because one of its conditions is not satisfied.

## Service State Is Not Application Health

A service being `active` proves that systemd currently considers the service running.

It does not prove that:

- the expected network socket is listening
- the application is responding correctly
- a firewall permits the traffic
- the network path works
- a remote client can reach the application

Mental model:

    service state ≠ application health ≠ network reachability

Test each relevant layer independently.

INC-0004 demonstrated this with Apache: `httpd` could be active while the application returned an HTTP error, and later the application could work locally while remote access was still blocked by firewalld.

## Remember

- `systemctl` is used to inspect and control systemd units.
- `active` means running now.
- `enabled` means configured for automatic startup.
- Active and enabled are separate properties.
- Inspect a failed service before changing it.
- `systemctl status` gives detailed current information.
- `systemctl cat` shows the unit configuration.
- `systemctl --failed` lists failed units.
- `start` and `stop` affect runtime state.
- `enable` and `disable` affect automatic startup configuration.
- `reset-failed` clears the recorded failure; it does not fix the cause.
- After a change, verify the result.
