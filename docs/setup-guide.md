# Setup Guide

This guide describes the main configuration steps for a Raspberry Pi 5 homelab running AdGuard Home and Tailscale.

The instructions are based on a personal setup. Some network settings may differ depending on the router and operating system.

## 1. Requirements

* Raspberry Pi 5
* Raspberry Pi OS
* A stable internet connection
* SSH access to the Raspberry Pi
* A Tailscale account
* AdGuard Home

## 2. Install AdGuard Home

Follow the official installation guide:

https://adguard-dns.io/kb/adguard-home/getting-started/

After installation, open the AdGuard Home web interface and complete the initial setup.

Configure the DNS service to listen on the intended network interfaces. Do not expose the DNS service directly to the public internet.

## 3. Configure Upstream DNS

Use Quad9 DNS-over-HTTPS as the upstream resolver:

```text
https://dns10.quad9.net/dns-query
```

DNS-over-HTTPS encrypts DNS queries between AdGuard Home and the upstream resolver.

## 4. Configure DNS Blocklists

The configuration uses the following lists:

* AdGuard DNS filter
* HaGeZi Multi PRO++

Official HaGeZi Multi PRO++ list:

https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/pro.plus.txt

Multi PRO++ offers more aggressive filtering than the standard PRO list. Some legitimate domains may occasionally be blocked, so review the query log when troubleshooting.

## 5. Install Tailscale

Follow the official installation instructions:

https://tailscale.com/docs/install

Sign in to your tailnet and connect the Raspberry Pi.

The Raspberry Pi can then be accessed using its Tailscale address from approved devices.

## 6. Configure Tailscale DNS

In the Tailscale admin console:

1. Open the DNS settings.
2. Add the Raspberry Pi's Tailscale IP address as a global nameserver.
3. Enable DNS server override if clients should use the configured nameserver.
4. Configure each client to use Tailscale DNS settings.

Only clients using this DNS configuration will benefit from the AdGuard filtering service.

## 7. Configure Secure SSH Access

The server uses SSH public-key authentication.

The intended security configuration includes:

* Public-key authentication enabled.
* Password-based SSH login disabled.
* Direct root login disabled.

Keep a working SSH session and verify key-based access before changing SSH settings. Incorrect configuration can lock out remote administration.

Never publish private SSH keys.

## 8. Configure Automated Backups

A systemd service and timer are used to run scheduled backups.

The backup includes selected service configuration and data needed for recovery.

Store an additional backup outside the Raspberry Pi when possible. Test the restore procedure before relying on a backup during an outage.

Do not publish backup archives or unredacted configuration files.

## 9. DNS Validation

Replace `<PI_LAN_IP>` with the Raspberry Pi's local network address.

Resolve a normal domain:

```bash
nslookup example.com <PI_LAN_IP>
```

A successful query should return normal IP addresses.

Test a domain expected to be blocked by the configured lists:

```bash
nslookup doubleclick.net <PI_LAN_IP>
```

A blocked response may return `0.0.0.0` and `::`, depending on the filter and DNS configuration.

For clients using Tailscale DNS, test both normal domain resolution and a known blocked domain while Tailscale is connected.

## 10. Network Design

The home router uses Quad9 directly for its default DNS service. This prevents ordinary home internet access from depending entirely on the Raspberry Pi.

Selected Tailscale-connected clients can use the Raspberry Pi for DNS filtering.

A client configured to use the Raspberry Pi as its DNS resolver may experience DNS failures when the Raspberry Pi is unavailable. Test the intended fallback behavior for each client.

## Limitations

* DNS filtering cannot block every advertisement.
* Some applications use their own DNS or content-delivery mechanisms.
* Aggressive blocklists may cause false positives.
* DNS-over-HTTPS encrypts the connection to the upstream resolver, but does not provide complete anonymity.
* Tailscale provides private connectivity but does not eliminate all forms of tracking.

## References

* AdGuard Home: https://adguard-dns.io/kb/adguard-home/getting-started/
* Tailscale installation: https://tailscale.com/docs/install
* Tailscale DNS: https://tailscale.com/docs/reference/dns-in-tailscale
* HaGeZi DNS blocklists: https://github.com/hagezi/dns-blocklists
* Quad9: https://quad9.net/
