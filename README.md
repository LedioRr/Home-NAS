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

    Homeserver DNS: 192.xxxxxx

    Public fallback DNS: xxxxxxx

    Devices not configured manually continue using the router's default DNS.

Additional Considerations

    Android Private DNS can bypass manually configured DNS and must be disabled on devices where filtering is required.

    Browser-level DNS-over-HTTPS (DoH) can also bypass local DNS filtering.

    Firewalld must allow TCP/UDP port xx for DNS queries from LAN devices.

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

# Phase 3 -- Private Web & Media Services

## Objective

Extend the NAS from a storage server into a private media and web-access platform while keeping all services accessible only through the Tailscale private network.

### Planned Services

* File Browser / web-based file access
* Movie and TV streaming
* Photo viewing and management
* Music streaming
* Docker-based service deployment

### Architecture Model

Host OS (openSUSE)
↓
Docker
↓
Private Web & Media Services
↓
Tailscale Private Network
↓
Authorized Devices

### Access Model

* No public internet exposure
* No router port forwarding
* Services reachable through Tailscale only
* Access restricted to authorized Tailscale devices/users
* Each service remains isolated from the host where practical

This phase transitions the NAS from raw file access into a private personal cloud and media platform.

---

# Phase 4 -- Security Hardening

## Objective

Harden the NAS and its services while maintaining the Tailscale-only access model.

### Planned Improvements

* SSH key-only authentication
* Disable password SSH login
* Firewall configuration
* Fail2ban where appropriate
* Automatic security updates
* Service isolation
* Docker permission and filesystem review
* Tailscale access control
* Review exposed local ports and listening services

Public internet exposure is intentionally excluded from the project scope.

---

# Phase 5 -- Observability & Reliability

## Objective

Create centralized monitoring for the NAS and all hosted services.

### Planned Improvements

* Uptime Kuma / service health monitoring
* Prometheus experimentation
* Node Exporter
* Grafana dashboards
* CPU, RAM and storage monitoring
* Disk health monitoring
* Service availability monitoring
* Centralized logging
* Alerting and notifications
* Backup automation
* Disaster recovery plan

### Monitoring Model

Service
↓
Health Check / Metrics / Logs
↓
Monitoring System
↓
Alert
↓
Notification

The monitoring layer should make it possible to identify service failures before they become larger infrastructure problems.

---

# Phase 6 -- Local AI Operations Assistant

## Objective

Use a locally hosted AI model to assist with monitoring, diagnostics and troubleshooting without sending NAS data or logs to external AI services.

### Planned Technology

* Local LLM runtime
* Small quantized language model
* Local documentation / knowledge base
* Integration with monitoring and system logs

### AI Responsibilities

* Analyze monitoring alerts
* Analyze relevant service logs
* Identify likely causes of simple failures
* Explain technical errors in understandable language
* Provide step-by-step troubleshooting instructions
* Reference the NAS documentation when recommending solutions

### Safety Model

The AI initially operates in read-only mode.

AI
↓
Analyze
↓
Explain
↓
Recommend
↓
User approval

Automated system changes are not performed without explicit approval.

---

# Phase 7 -- Controlled Automation & Recovery

## Objective

Automate recovery of simple, predefined problems while maintaining strict control over system changes.

### Planned Improvements

* Automated health checks
* Predefined recovery procedures
* Controlled service restarts
* Post-recovery verification
* Failure escalation
* Notification after recovery or failed recovery

### Recovery Model

Service failure
↓
Monitoring detects failure
↓
AI analyzes condition
↓
Predefined recovery action
↓
Health check
↓
Success → notification
Failure → notify user

Only explicitly defined and tested recovery actions will be automated.


# DevOps Skills Practiced

-   Linux system administration
-   Filesystem management
-   Secure networking
-   Infrastructure automation
-   Observability implementation
-   Threat surface reduction
-   Incremental system evolution mindset

------------------------------------------------------------------------


