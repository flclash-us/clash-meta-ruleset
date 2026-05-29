# Encrypted DNS Guide 79shxp

A comprehensive guide to setting up encrypted DNS (DNS-over-HTTPS, DNS-over-TLS, DNS-over-QUIC) for enhanced privacy and security.

## Why Encrypted DNS?

Standard DNS queries are sent in plain text, allowing ISPs and network operators to:
- See every website you visit
- Block or redirect domains
- Build detailed browsing profiles

Encrypted DNS solves this by wrapping queries in TLS/QUIC encryption.

## Protocol Comparison

| Protocol | Port | Encryption | Performance | Support |
|----------|------|-----------|-------------|---------|
| DoH | 443 | TLS 1.3 | Good | Wide |
| DoT | 853 | TLS 1.3 | Good | Moderate |
| DoQ | 853 | QUIC | Excellent | Growing |

## Quick Setup with Clash

Add to your Clash configuration:

```yaml
dns:
  enable: true
  listen: 0.0.0.0:53
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.1/16
  
  default-nameserver:
    - 223.5.5.5
    - 119.29.29.29
    
  nameserver:
    - https://dns.alidns.com/dns-query
    - https://doh.pub/dns-query
    - tls://dns.alidns.com:853
    
  fallback:
    - https://1.1.1.1/dns-query
    - https://dns.google/dns-query
    - tls://8.8.8.8:853
    
  fallback-filter:
    geoip: true
    geoip-code: CN
```

## System-Level Setup

### Windows
```powershell
# Using PowerShell (Admin)
# Set DoH for Ethernet adapter
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses ("1.1.1.1","1.0.0.1")
```

### macOS
```bash
# Install dnscrypt-proxy
brew install dnscrypt-proxy
# Copy example config
cp /opt/homebrew/etc/dnscrypt-proxy.toml.example /opt/homebrew/etc/dnscrypt-proxy.toml
# Start service
brew services start dnscrypt-proxy
```

### Linux
```bash
# Install stubby for DoT
apt install stubby
# Configure DoT servers
cat >> /etc/stubby/stubby.yml << EOF
upstream_recursive_servers:
  - address_data: 1.1.1.1
    tls_auth_name: "cloudflare-dns.com"
  - address_data: 8.8.8.8
    tls_auth_name: "dns.google"
EOF
systemctl enable stubby && systemctl start stubby
```

## Recommended Public DNS

| Provider | DoH | DoT | Privacy | Speed |
|----------|-----|-----|---------|-------|
| Cloudflare | 1.1.1.1 | Yes | Good | Fast |
| Google | 8.8.8.8 | Yes | Moderate | Fast |
| AliDNS | 223.5.5.5 | Yes | Good (CN) | Fast (CN) |
| AdGuard | 94.140.14.14 | Yes | Good | Good |

## Recommended Tools

- [ClashMI](https://clashmi.site/) - Best proxy client for Windows/Mac/Linux
- [Clash for Windows](https://clashforwindows.site/) - Most popular Clash GUI
- [ClashMI](https://clashmi.site/) - Lightweight Clash client
- [FlClash](https://flclash.us/) - Modern proxy tool

## License

MIT License
