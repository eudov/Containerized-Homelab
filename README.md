# Containerized-Homelab
Docker-based homelab stack - VPN, Recursive DNS, and Reverse Proxy

*Please Note:
    - this project was created for and deployed on a raspberry pi 5 for a personal homelab setup. Your milage may vary!

Setup Instructions:
1. Clone the repository
2. Copy `.env.example` to `.env`
3. Fill in your values in `.env`:
   - Replace `your_secure_password_here` with your Pi-hole password
   - Replace `your.public.ip.address` with your WireGuard server URL
   - etc.
4. Run `docker-compose up -d`