# Containerized-Homelab
Simple but powerful Docker-based homelab stack for privacy, security, and local DNS services.
Features include VPN, Recursive DNS, Local DNS, DNS Sinkhole (Network level Ad and Tracker Blocker), and Reverse Proxy.
Created for personal use and deployed on a raspberry pi 5.

## Architecture Overview

```
                          Internet
                             │
                ┌────────────┴────────────┐
                │                         │
          WireGuard VPN              HTTPS Traffic
          (Port 51820)               (Port 443)
                │                         │
                └────────────┬────────────┘
                             │
          ┌──────────────────▼──────────────────┐
          │    Docker Container Stack          │
          │                                     │
          │  Nginx Proxy Manager                │
          │  ├─ Reverse proxy (80/443)          │
          │  └─ SSL/TLS management             │
          │         ↓                            │
          │  Pihole (Port 53)                  │
          │  ├─ DNS filtering & blocking        │
          │  └─ Web UI (Port 8080)              │
          │         ↓                            │
          │  Unbound (Port 5335)                │
          │  ├─ Recursive DNS resolver          │
          │  └─ DNSSEC validation               │
          │         ↓                            │
          │  Root Nameservers                   │
          │                                     │
          └─────────────────────────────────────┘
```
Features:
- Nginx Proxy Manager, acting as a configurable front door for the whole stack. I mainly use this for configuring SSL and local DNS, but it's a very powerful tool.
- The Pihole acts as a DNS sinkhole, referring undesirable traffic to the internet void. This blocks many (but not all!) ads, trackers, and even potentially malicious requests. It can be configured to operate at a router level, blocking requests from smart devices that do not support plugin adblockers.
- Unbound provides DNS security and some privacy features. It acts as a recursive DNS resolver, meaning that it goes down the entire referral path until it confirms with the authoritative nameserver that it's correct, which can prevent spoofing and means that no individual company will have your entire query history. Note that your ISP can see your requests, but not the DNS servers. A traditional VPN service is still better if the goal is to obscure your traffic. 
- WireGuard allows you to tunnel in to access any part of your network remotely, including all the DNS features - Pihole on the go!

*Please Note:
    - This project was created for and deployed on a raspberry pi 5 for a personal homelab setup. I would actually recommend most small linux servers, it's just what I had lying around.

## Configuration
Open `.env` and update these values:
   - **PIHOLE_PASSWORD**: Login for Pi-hole web application
   - **WIREGUARD_SERVER_URL**: Your public IP for WireGuard
   - **WIREGUARD_PEERS**: Devices to tunnel to WireGuard (phone, laptop... etc)
   - **WIREGUARD_DNS**: DNS servers to use when connected to VPN (set this to your pihole device's ip)
   - **PIHOLE_ADLISTS**: Blocklists for ad-blocking (already has Pihole default)
    - **TZ**: Your timezone

## Quick Start
```
# 1. Clone and setup
git clone https://github.com/eudov/Containerized-Homelab.git
cd Containerized-Homelab
cp .env.example .env

# 2. Edit .env with your values
nano .env  # or your preferred editor

# 3. Start the stack
docker-compose up -d

# 4. Verify services are running
docker-compose ps

# 5. Access the services and configure
# Pi-hole Web UI: http://localhost:8080 (login with PIHOLE_PASSWORD)
# Nginx Manager: http://localhost:81
# WireGuard: Use generated config files
