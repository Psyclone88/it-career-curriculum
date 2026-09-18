# Users, sudo, Privileges and Permissions

## User Identity

Linux processes run as a user.

Useful command:

    id

Example:

    id

This shows information including:

- my user ID (UID)
- my primary group
- other groups I belong to

My normal Fedora account is:

    cipher

Normal administrative work should generally be performed from my regular account rather than by staying logged in as root.

## root

`root` is the traditional Linux superuser account.

It has very broad administrative privileges and normally has UID 0.

Because root has extensive control over the system, commands executed with root privileges need extra care.

Mental model:

    normal user → limited privileges
    root        → administrative privileges

## sudo

`sudo` allows an authorised user to run a command with another user's privileges, normally root's.

Example:

    sudo dnf install PACKAGE

The command after `sudo` runs with elevated privileges.

Important:

    sudo changes the privilege context of the command.

It does NOT mean that Linux permissions have disappeared.

Permissions still exist; the command is simply being executed with a more privileged identity.

## Groups and Administrative Access

Group membership can help determine what a user is authorised to do.

On Fedora, membership of the `wheel` group is commonly used to grant administrative access through sudo.

Useful command:

    id

If my account belongs to `wheel`, that is evidence that it may be authorised to use sudo, subject to the system's sudo configuration.

## Permissions vs Privileges

Permissions describe what access is allowed to resources such as files and directories.

Useful command:

    ls -l FILE

Example permission display:

    -rw-r--r--

Linux file permissions are commonly divided between:

    user    → owner of the file
    group   → group associated with the file
    other   → everyone else

The basic permission types are:

    r → read
    w → write
    x → execute

Privileges are broader authority granted to a user or process.

These concepts are related, but they are not identical.

Example:

A normal user may be denied access to a protected resource because of its ownership or permissions.

Running an appropriate command through `sudo` gives that command elevated privileges, which may allow it to access the resource.

Mental model:

    permissions → what access a resource allows
    privileges  → authority of the user/process attempting the action

## Troubleshooting Access Problems

Do not automatically use `sudo` whenever a command says:

    Permission denied

First ask:

    Who am I?
    What am I trying to access?
    Who owns it?
    What are its permissions?
    Should my normal user actually have access?

Useful commands include:

    id
    ls -l FILE
    ls -ld DIRECTORY

Use `sudo` when elevated privileges are genuinely required, not merely as a way to make an unexplained error disappear.

## Remember

- `id` shows my user and group identity.
- `root` is the traditional superuser.
- `sudo` runs an authorised command with elevated privileges.
- `sudo` does not mean permissions cease to exist.
- Fedora commonly uses the `wheel` group for sudo-enabled administrators.
- File access depends on ownership and permissions.
- `r`, `w`, and `x` mean read, write, and execute.
- Investigate a permission problem before reaching automatically for `sudo`.


