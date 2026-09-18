# Quick Reference

This file contains things I currently want available at a glance.

It should stay small.

When something becomes automatic, remove it from here. Detailed explanations belong in the subject notes.

## Networking

    Fedora interface:  wlp5s0
    Fedora IP:         192.168.2.119/24
    Home network:      192.168.2.0/24
    Home gateway:      192.168.2.1

    Career lab:        10.20.0.0/24
    Lab gateway:       10.20.0.1
    lab-srv01:         10.20.0.106/24

    127.0.0.1          → loopback / this machine
    DHCP               → supplies network configuration
    DNS                → resolves names to addresses

Inspect interfaces:

    ip -brief address

Inspect routes:

    ip route

Troubleshooting order:

    interface/address
        ↓
    local network
        ↓
    gateway
        ↓
    external IP
        ↓
    DNS/name

## systemd

    active  → running now
    enabled → configured for automatic startup

Inspect:

    systemctl status SERVICE
    systemctl is-active SERVICE
    systemctl is-enabled SERVICE
    systemctl --failed
    systemctl cat SERVICE

Rule:

    investigate → understand → change → verify

## Git

    git status
    git diff
    git add PATH
    git diff --cached
    git commit -m "message"
    git log --oneline
    git push

Workflow:

    inspect
      ↓
    stage deliberately
      ↓
    inspect staged changes
      ↓
    commit
      ↓
    verify
      ↓
    push

Remember:

    commit → local repository
    push   → remote repository

## Linux

    pwd          → where am I?
    ls           → what is here?
    cd PATH      → change directory
    cd ..        → parent directory
    cat FILE     → display a text file
    id           → user/group identity

    sudo → run an authorised command with elevated privileges

Do not automatically add `sudo` to an unexplained permission error.

## Current Technician Habit

    symptom
      ↓
    evidence
      ↓
    hypothesis
      ↓
    test
      ↓
    conclusion
      ↓
    controlled change
      ↓
    verification
