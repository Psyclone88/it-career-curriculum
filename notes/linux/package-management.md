# Fedora Package Management

Fedora primarily uses DNF for package management. RPM provides lower-level tools for inspecting installed RPM packages.

## Checking Whether a Package Is Installed

Use:

    rpm -q PACKAGE

Example:

    rpm -q mcelog

If installed, RPM reports the installed package version.

If it is not installed, RPM reports that the package is not installed.

## Inspecting an Installed Package

Use:

    rpm -qi PACKAGE

Example:

    rpm -qi mcelog

This displays package information such as:

- name and version
- architecture
- installation date
- package description
- source and vendor information

To list documentation installed by a package:

    rpm -qd PACKAGE

Example:

    rpm -qd mcelog

This can help locate man pages and other documentation.

## Finding Installed Dependencies

To investigate whether another installed package requires a package:

    sudo dnf repoquery --installed --whatrequires PACKAGE

Example:

    sudo dnf repoquery --installed --whatrequires mcelog

No output can itself be useful evidence: it means the query found no installed package declaring that package as a requirement.

Do not interpret that alone as proof that removing the package is safe. Other configuration or operational reasons may still matter.

## DNF Transaction History

DNF records package-management transactions.

To inspect recent transactions:

    dnf history list

A transaction has an ID that can be inspected in more detail:

    sudo dnf history info ID

Example:

    sudo dnf history info 1

This can help answer questions such as:

- When was a package installed?
- Was it installed, upgraded, or removed?
- Which transaction changed it?
- Was it installed explicitly, as a dependency, or as part of a group?

To find transactions involving a particular package, check the available DNF history options.

Useful command:

    dnf history list --help

During INC-0002 this led to:

    sudo dnf history list --contains-pkgs=mcelog

We then inspected the matching transaction and discovered that `mcelog` had been installed by the Fedora Server installer as part of a package group.

## Using Built-in Help

If a command or option does not work, do not keep guessing variations.

Check the program's own documentation:

    COMMAND --help
    man COMMAND

Examples:

    dnf history list --help
    man dnf
    man rpm

Built-in help is an important troubleshooting tool.

A failed command can provide useful evidence:

    command fails
        ↓
    read the error
        ↓
    inspect --help or man page
        ↓
    correct the command
        ↓
    verify the result

## Before Installing or Removing Packages

DNF normally presents a transaction summary before making package changes.

Read it.

Check:

- what will be installed
- what will be removed
- what will be upgraded
- additional dependencies
- whether the proposed change matches the intended task

Do not automatically press `y`.

Mental model:

    Request change → inspect transaction → understand impact → approve if correct

## Query First, Change Second

Package-management commands can either inspect the system or change it.

Examples of inspection:

    rpm -q PACKAGE
    rpm -qi PACKAGE
    rpm -qd PACKAGE
    dnf history list
    dnf repoquery ...

Examples that can change the system:

    sudo dnf install PACKAGE
    sudo dnf remove PACKAGE
    sudo dnf upgrade

When troubleshooting, prefer gathering evidence before making a state-changing package operation.

Mental model:

    Inspect → understand → decide → change → verify

## Remember

- `rpm -q` checks whether an RPM package is installed.
- `rpm -qi` shows information about an installed package.
- `rpm -qd` locates package documentation.
- `dnf repoquery` can answer questions about package relationships.
- `dnf history` helps reconstruct previous package changes.
- An empty query result can still be meaningful evidence.
- Use `--help` and man pages instead of guessing command syntax.
- Read DNF's transaction summary before approving changes.
- Package dependency information is evidence, not automatic proof that removal is safe.
- Prefer inspection before modification.
