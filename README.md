# pihole-docker-homelab
Details of Pi-hole configuration created in docker
The goal of this homelab is to demonstrate practical skills in:

* Linux command-line administration
* Docker & containerised services
* Networking & DNS fundamentals
* Secure configuration management (secrets handling)
* Git & GitHub workflows

This repository is designed to be portfolio-ready and reproducible.

---

## Architecture

* **Host**: Raspberry Pi running Linux
* **Container runtime**: Docker
* **Service**: Pi-hole (DNS filtering & ad blocking)
* **Networking**: Host networking mode (binds directly to port 53)

```
Client Devices
     │
     ▼
 Raspberry Pi (Host Network)
     │
     ▼
  Pi-hole Docker Container
     │
     ▼
 Upstream DNS (Cloudflare / Google / ISP)
```

---

## Project Structure

```
homelab/pihole/
├── docker-compose.yml      # Container definition
├── .gitignore              # Prevents secrets & runtime files being committed
├── .env.example            # Example environment variables (safe for GitHub)
├── etc-pihole/             # Persistent Pi-hole configuration
│   ├── adlists.list
│   ├── custom.list
│   ├── whitelist.txt
│   ├── blacklist.txt
│   └── regex.list
└── etc-dnsmasq.d/           # DNS configuration
```

---

## docker-compose.yml

Key design decisions:

* **Host networking** – required for Pi-hole to bind to DNS ports (53/UDP)
* **Persistent volumes** – configuration survives container restarts
* **Environment file** – secrets are never committed to Git

```yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    restart: unless-stopped
    network_mode: "host"
    env_file:
      - .env
    volumes:
      - "./etc-pihole/:/etc/pihole/"
      - "./etc-dnsmasq.d/:/etc/dnsmasq.d/"
    cap_add:
      - NET_ADMIN
```

---

## Environment Variables

Secrets are stored locally in a `.env` file (ignored by Git):

```
WEBPASSWORD=your_secure_password
```

An example file is provided:

```
.env.example
```

---

## Git Hygiene & Security

This project deliberately avoids committing:

* Runtime databases (`pihole-FTL.db*`)
* Logs
* Secrets (`.env`, `setupVars.conf`)

These are excluded via `.gitignore`:

```text
.env
etc-pihole/setupVars.conf
etc-pihole/pihole-FTL.db*
etc-pihole/logrotate
*.log
```

This ensures the repository is safe for public sharing.

---

## Common Commands

### Start Pi-hole

```bash
docker-compose up -d
```

### Stop Pi-hole

```bash
docker-compose down
```

### View container status

```bash
docker ps
```

### Check logs

```bash
docker logs pihole
```

### Git workflow

```bash
git status
git add .
git commit -m "Update configuration"
git pull --rebase origin main
git push origin main
```

---

## What This Demonstrates

* Practical Docker usage without abstractions
* Understanding of DNS and networking requirements
* Secure handling of secrets in version control
* Ability to document and structure a real-world homelab

This project reflects ongoing self-study in **cyber security, infrastructure, and systems engineering**.

---

## Future Improvements

* Add monitoring (Prometheus / Grafana)
* Add unbound for recursive DNS
* Extend to multi-service homelab (VPN, reverse proxy)
* Harden with firewall rules

---

## Disclaimer

This repository contains **configuration only**. No credentials or personal data are included.
