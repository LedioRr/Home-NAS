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
-   Memory usage percentage Phase 2.1 -- DNS Ad & Tracker Filtering
Objective

Extend the NAS infrastructure to provide DNS-level ad and tracker filtering for selected devices on the home network without exposing the server publicly.
Filtering Architecture

    DNS filtering using dnsmasq

    StevenBlack hosts blocklist

    79,994 ad/tracker domains loaded into the local blocklist

    Weekly automatic blocklist updates via cron

    DNS service runs directly on the homeserver

    Tailscale remains the private remote-access layer

Implementation

    Initially evaluated Pi-hole, but switched to a lightweight dnsmasq + script implementation.

    The filtering script installs and maintains the blocklist in /etc/dnsmasq.d/blocklist.conf.

    Weekly updates are automated through /etc/cron.d/ad-blocker-update.

    Custom dnsmasq logging was removed after a startup issue with the custom log file; logging now uses the systemd journal.

Verification

DNS filtering was tested locally:

    doubleclick.net → blocked and returned 0.0.0.0

    google.com → resolved normally

    DNS queries from configured devices confirmed reaching dnsmasq

Device Configuration

The ISP-provided Nokia router does not allow modification of its DHCP/DNS settings, so network-wide DNS enforcement is not currently possible.

Instead, selected devices are manually configured to use the homeserver as their DNS server:

    Homeserver DNS: 192.168.1.43

    Public fallback DNS: 1.1.1.1

    Devices not configured manually continue using the router's default DNS.

Additional Considerations

    Android Private DNS can bypass manually configured DNS and must be disabled on devices where filtering is required.

    Browser-level DNS-over-HTTPS (DoH) can also bypass local DNS filtering.

    Firewalld must allow TCP/UDP port 53 for DNS queries from LAN devices.

    The homeserver currently uses a DHCP-assigned IP, so configuring a static IP directly on the openSUSE system is the next step to prevent DNS configuration from breaking if the address changes.

This phase adds network-level filtering while keeping file storage, SMB access, and Tailscale-based remote access separated from the DNS service.
-   Daily email reporting via SMTP (msmtp) ✔️ 
-   Cron-based automation ✔️ 

### Architecture
Set up my home NAS on an old laptop with SuSe Linux Server and Tailscale for secure remote access. Configured msmtp and systemd scripts to send automatic boot and disk usage alert emails. Disabled sleep, suspend, and screen locking to keep the system always online while the display turns off. Automated daily disk checks via systemd, and verified all scripts and notifications work reliably. The NAS is now fully accessible remotely, with persistent background services and low-power idle mode.

------------------------------------------------------------------------

# Phase 2 -- Multi-User Secure Storage

## Objective

Enable multiple users with isolated private storage. ✔️ 

### Filesystem Layout

/home/storage/ users/ xxxx/ user2/ user3/ shared/

### Security Model

-   Individual Linux user accounts
-   700 permissions for private directories
-   Strict ownership controls
-   Optional shared directory

This introduces real Linux access control and storage isolation.

------------------------------------------------------------------------
# Phase 2.1 -- DNS Ad & Tracker Filtering

## Objective

Extend the NAS infrastructure to provide DNS-level ad and tracker filtering for selected devices on the home network without exposing the server publicly.

### Filtering Architecture

-   DNS filtering using dnsmasq ✔️ 
-   StevenBlack hosts blocklist ✔️ 
-   79,994 ad/tracker domains loaded into the local blocklist ✔️ 
-   Weekly automatic blocklist updates via cron ✔️ 
-   DNS service runs directly on the homeserver ✔️ 
-   Tailscale remains the private remote-access layer ✔️ 

### Implementation

Initially evaluated Pi-hole, but switched to a lightweight dnsmasq + script implementation.
The filtering script installs and maintains the blocklist in /etc/dnsmasq.d/blocklist.conf.
Weekly updates are automated through /etc/cron.d/ad-blocker-update.
Custom dnsmasq logging was removed after a startup issue with the custom log file; logging now uses the systemd journal.

### Verification

DNS filtering was tested locally:

-   doubleclick.net → blocked and returned 0.0.0.0
-   google.com → resolved normally
-   DNS queries from configured devices confirmed reaching dnsmasq
  
### Device Configuration

The ISP-provided Nokia router does not allow modification of its DHCP/DNS settings, so network-wide DNS enforcement is not currently possible.

Instead, selected devices are manually configured to use the homeserver as their DNS server:

-   Homeserver DNS: 192.168.x.xx
-   Public fallback DNS: 1.1.1.1
-   Devices not configured manually continue using the router's default DNS.

### Additional Considerations

-   Android Private DNS can bypass manually configured DNS and must be disabled on devices where filtering is required.
-   Browser-level DNS-over-HTTPS (DoH) can also bypass local DNS filtering.
-   Firewalld must allow TCP/UDP port XX for DNS queries from LAN devices.
-   The homeserver currently uses a DHCP-assigned IP, so configuring a static IP directly on the openSUSE system is the next step to prevent DNS configuration from breaking if the address changes.

This phase adds network-level filtering while keeping file storage, SMB access, and Tailscale-based remote access separated from the DNS service.

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


