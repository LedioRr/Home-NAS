#  Homelab Infrastructure

## Overview

This repository documents my self-hosted infrastructure lab focused on
secure, low-cost, and automation-driven deployments using Linux-based
systems.

The lab simulates real-world DevOps concepts including:

-   Infrastructure design
-   Private networking
-   Multi-user architecture
-   Monitoring & observability
-   Security hardening
-   Automation
-   Incremental system evolution

------------------------------------------------------------------------

# Infrastructure Architecture

Core design principle:

No unnecessary public exposure. Minimal attack surface. Controlled
access.

Primary Node: - HP EliteBook 820 G3 (openSUSE Leap) - External HDD
storage - Tailscale private mesh VPN

------------------------------------------------------------------------

# Phase 1 -- On-Demand Infrastructure

## Objective

Transform NAS into an energy-efficient, remotely controlled server.

### Smart Plug Integration

-   Remote power control via WiFi smart plug ✔️ 
-   BIOS: "Power On After AC Loss" enabled  ✖️ not possible on this laptop version
-   Server runs only when needed ✔️ 

### Operational Flow

1.  Enable smart plug remotely ✔️ 
2.  Laptop auto-boots ✖️
3.  Tailscale reconnects ✔️ 
4.  Services become available ✔️ 

This reduces cost and introduces infrastructure lifecycle control.

------------------------------------------------------------------------

# Phase 1.1 -- Basic Monitoring & Reporting

## Monitoring Scope

-   Disk usage percentage ✔️ 
-   Memory usage percentage ✔️ 
-   Daily email reporting via SMTP (msmtp) ✔️ 
-   Cron-based automation ✔️ 

### Architecture

Cron Job ↓ Monitoring Script ↓ Metric Collection ↓ SMTP Client ↓ Email
Notification

Future Enhancements: - Threshold-based alerts (\>80% disk) - SMART disk
health checks - Log rotation - Historical logging

------------------------------------------------------------------------

# Phase 2 -- Multi-User Secure Storage

## Objective

Enable multiple users with isolated private storage.

### Filesystem Layout

/home/storage/ users/ xxxx/ user2/ user3/ shared/

### Security Model

-   Individual Linux user accounts
-   700 permissions for private directories
-   Strict ownership controls
-   Optional shared directory

This introduces real Linux access control and storage isolation.

------------------------------------------------------------------------

# Phase 3 -- Web-Based Access Layer

## Planned Technology

-   Docker
-   Nextcloud container
-   Reverse proxy (future phase)

### Architecture Model

Host OS (openSUSE) ↓ Docker ↓ Nextcloud Container ↓ Reverse Proxy ↓ VPN
or Controlled Internet Access

This transitions from raw file access (SFTP) to application-layer
access.

------------------------------------------------------------------------

# Phase 4 -- Security Hardening

Before public exposure:

-   SSH key-only authentication
-   Disable password SSH login
-   Firewall configuration
-   Fail2ban
-   HTTPS with Let's Encrypt
-   Automatic updates

Public exposure will only be enabled after hardening is complete.

------------------------------------------------------------------------

# Phase 5 -- Observability & Reliability

Planned Improvements:

-   Prometheus experimentation
-   Node Exporter
-   Grafana dashboards
-   Centralized logging
-   Backup automation
-   Disaster recovery plan

------------------------------------------------------------------------

# DevOps Skills Practiced

-   Linux system administration
-   Filesystem management
-   Secure networking
-   Infrastructure automation
-   Observability implementation
-   Threat surface reduction
-   Incremental system evolution mindset

------------------------------------------------------------------------


