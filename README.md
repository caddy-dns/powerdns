# PowerDNS DNS Provider for Caddy

[![Go Reference](https://pkg.go.dev/badge/github.com/victories/powerdns.svg)](https://pkg.go.dev/github.com/victories/powerdns)

This package contains a DNS provider module for [Caddy](https://github.com/caddyserver/caddy). It can be used to manage DNS records with PowerDNS.

> **⚠️ Fixed Version:** This fork uses the patched [victories/libdns-powerdns](https://github.com/victories/libdns-powerdns) which is compatible with Caddy v2.10+ and libdns v1.0.

## 🚀 Quick Start

### Basic Installation

```bash
xcaddy build v2.10.2 \
    --with github.com/victories/powerdns@latest
```

### With Common Security Plugins

```bash
xcaddy build v2.10.2 \
    --with github.com/victories/powerdns@latest \
    --with github.com/shift72/caddy-geo-ip \
    --with github.com/steffenbusch/caddy-bot-barrier \
    --with git.gorbe.io/caddy/geoip \
    --with github.com/WeidiDeng/caddy-cloudflare-ip \
    --with github.com/deanchou/caddy_ip_filter
```

### Alternative with CrowdSec

```bash
xcaddy build v2.10.2 \
    --with github.com/victories/powerdns@latest \
    --with github.com/hslatman/caddy-crowdsec-bouncer/http \
    --with github.com/shift72/caddy-geo-ip
```

## 📝 Configuration

### Caddyfile Syntax

**Basic:**
```caddyfile
tls {
    dns powerdns {env.POWERDNS_SERVER_URL} {env.POWERDNS_API_TOKEN}
}
```

**Extended:**
```caddyfile
tls {
    dns powerdns {
        server_url https://dns.example.com:8081
        api_token {env.POWERDNS_API_TOKEN}
        server_id localhost
    }
}
```

### Complete Example with GeoIP and Bot Protection

```caddyfile
{
    order geoip before bot_barrier
}

example.com {
    tls {
        dns powerdns {
            server_url https://dns.example.com:8081
            api_token {env.POWERDNS_API_TOKEN}
        }
    }
    
    # GeoIP blocking
    geoip {
        db_path /path/to/GeoLite2-Country.mmdb
    }
    
    @blocked_countries {
        expression `{geoip.country_code} in ['CN', 'RU', 'KP']`
    }
    
    respond @blocked_countries 403
    
    # Bot protection
    bot_barrier {
        block_ua "BadBot"
    }
    
    reverse_proxy localhost:8080
}
```

### Wildcard Certificates

```caddyfile
*.example.com {
    tls {
        dns powerdns {env.POWERDNS_SERVER_URL} {env.POWERDNS_API_TOKEN}
    }
    
    @app1 host app1.example.com
    handle @app1 {
        reverse_proxy localhost:8001
    }
    
    @app2 host app2.example.com
    handle @app2 {
        reverse_proxy localhost:8002
    }
}
```

### JSON Configuration

```json
{
    "module": "acme",
    "challenges": {
        "dns": {
            "provider": {
                "name": "powerdns",
                "server_url": "{env.POWERDNS_SERVER_URL}",
                "api_token": "{env.POWERDNS_API_TOKEN}",
                "server_id": "localhost"
            }
        }
    }
}
```

## ⚙️ Configuration Options

| Parameter | Description | Required | Default |
|-----------|-------------|----------|---------|
| `server_url` | PowerDNS API endpoint | ✅ Yes | - |
| `api_token` | API authentication token | ✅ Yes | - |
| `server_id` | PowerDNS server identifier | ❌ No | `localhost` |

## 🔧 PowerDNS Setup

### 1. Enable API

Edit `/etc/powerdns/pdns.conf`:

```ini
api=yes
api-key=your-secret-api-key-here
webserver=yes
webserver-address=0.0.0.0
webserver-port=8081
webserver-allow-from=0.0.0.0/0,::/0
```

### 2. Restart Service

```bash
systemctl restart pdns
```

### 3. Verify API

```bash
curl -H "X-API-Key: your-secret-api-key-here" \
     http://localhost:8081/api/v1/servers/localhost/zones
```

## 🌍 Environment Variables

```bash
export POWERDNS_SERVER_URL="https://dns.example.com:8081"
export POWERDNS_API_TOKEN="your-api-token-here"
```

## 🔄 Migration from Local Build

**Before (with local paths):**
```bash
xcaddy build v2.10.2 \
    --with github.com/caddy-dns/powerdns=/root/powerdns-main \
    --with github.com/libdns/powerdns=/root/libdns-powerdns
```

**After (with GitHub):**
```bash
xcaddy build v2.10.2 \
    --with github.com/victories/powerdns@latest
```

No more local paths needed! 🎉

## 🆚 Differences from Original

This fork includes:

- ✅ **Updated for Caddy v2.10+**
- ✅ **Uses patched libdns-powerdns with v1.0 API support**
- ✅ **Fully tested and working**
- ✅ **Updated module path to victories/powerdns**
- ✅ **No local paths required**

## 📚 Related Repositories

- **libdns Provider:** [victories/libdns-powerdns](https://github.com/victories/libdns-powerdns)
- **Original Caddy Module:** [caddy-dns/powerdns](https://github.com/caddy-dns/powerdns)
- **PowerDNS:** [PowerDNS/pdns](https://github.com/PowerDNS/pdns)

## 🔌 Recommended Plugins

These plugins work well with this PowerDNS provider:

- **caddy-geo-ip** - GeoIP blocking
- **caddy-bot-barrier** - Bot protection
- **caddy-crowdsec-bouncer** - CrowdSec integration
- **caddy-cloudflare-ip** - Cloudflare real IP
- **caddy_ip_filter** - IP filtering

## 🤝 Contributing

Contributions welcome! Please ensure:
- Code is formatted with `go fmt`
- Changes are tested with Caddy v2.10+
- Pull requests have clear descriptions

## 📄 License

Apache License 2.0

## 🙏 Credits

- **Original:** [caddy-dns/powerdns](https://github.com/caddy-dns/powerdns)
- **Fixed by:** [@victories](https://github.com/victories)

---

**Note:** This fork will be maintained until the original repository is updated for Caddy v2.10+ compatibility.
