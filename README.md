# Tailscale DERP Ansible

> [!IMPORTANT]  
> This project is considered **feature-complete** — it already does what it is intended to do.  
> I may add small improvements or fixes when necessary, but no major new features are planned.  
> You are welcome to **contribute**, or **fork** the project to extend it further.

## Overview

This playbook helps you to self-host a **Tailscale DERP server** on Ubuntu/Debian. It is designed to run on **bare-metal**, since DERP does not work reliably behind firewalls, NAT, or load balancers. Custom DERP servers require direct, be on a publicly accessible network.

Tailscale does not provide automatic updates for DERP, so this playbook configures a **cron job** to keep it up to date ([Tailscale docs](https://tailscale.com/kb/1118/custom-derp-servers#limitations)).

For security, the playbook restricts access so that only DERP traffic is allowed.

This includes:

- self-update (Debian/Ubuntu only)
- ssh-hardening **(don't forget to add your SSH keys so you do not get kicked out)**
- firewall (UFW)
    - Allows only port 80, 443, 3478 (stun)
- fail2ban
    - includes configurable report to AbuseIPDB
- block blacklisted IPs, default is 3 (moderate), configurable from 1-8

## Custom Configuration

Adjust host-specific settings (domain, region, IP, etc.) directly in `inventory.ini` to suit your environment.

## Usage

```bash
git clone https://github.com/eznix86/tailscale-derper-ansible.git

cd tailscale-derper-ansible

# Edit inventory.ini as needed
cp inventory.ini.example inventory.ini

ansible-playbook -i inventory.ini derper.yaml
```

* `NOTES.txt` will be generated locally containing the DERP map snippet for Tailscale Access Control Panel.
* If you use cloudflare, **disable the proxy**.


## Debugging

To check if the DERP service on your node:

```bash
systemctl status derper
journalctl -u derper
```

## Basic sysadmin stuff

```bash
# look ips which are blocked
fail2ban-client status sshd
# firewall status
ufw status
# blocked list from https://github.com/stamparm/ipsum
ipset list
```

Verify if your DERP is accessible:

```bash
tailscale status # you should see what you specified as your derper_region_code
tailscale netcheck # if your derper is close to you, you should see it first
tailscale debug derp-map # you should see your config you added on tailscale
```

If you want to use the existing DERP provided by tailscale if yours is down, set `"OmitDefaultRegions"` to `false`, it will always take the first one based on `tailscale netcheck`.

For further things, RTFM: [https://tailscale.com/kb/1118/custom-derp-servers](https://tailscale.com/kb/1118/custom-derp-servers).

# Contributions

If you see something missing please contribute.

# License

[LICENSE](LICENSE)
