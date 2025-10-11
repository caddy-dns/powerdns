# PowerDNS DNS Provider for Caddy

[![Go Reference](https://pkg.go.dev/badge/github.com/victories/powerdns.svg)](https://pkg.go.dev/github.com/victories/powerdns)

This package contains a DNS provider module for [Caddy](https://github.com/caddyserver/caddy). It can be used to manage DNS records with PowerDNS.

> **⚠️ Fixed Version:** This fork uses the patched [victories/libdns-powerdns](https://github.com/victories/libdns-powerdns) which is compatible with Caddy v2.10+ and libdns v1.0.

## 🚀 Quick Start

### Installation with xcaddy

```bash
xcaddy build v2.10.0 \
    --with github.com/victories/powerdns@latest
```

### With Additional Plugins

```bash
xcaddy build v2.10.0 \
    --with github.com/victories/powerdns@latest \
    --with github.com/shift72/caddy-geo-ip \
    --with github.com/hslatman/caddy-crowdsec-bouncer/http
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

### Complete Example

```caddyfile
example.com {
    tls {
        dns powerdns {
            server_url https://dns.example.com:8081
            api_token {env.POWERDNS_API_TOKEN}
        }
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

## 🆚 Differences from Original

This fork includes:

- ✅ **Updated for Caddy v2.10+**
- ✅ **Uses patched libdns-powerdns with v1.0 API support**
- ✅ **Fully tested and working**
- ✅ **Updated module path to victories/powerdns**

## 📚 Related Repositories

- **libdns Provider:** [victories/libdns-powerdns](https://github.com/victories/libdns-powerdns)
- **Original Caddy Module:** [caddy-dns/powerdns](https://github.com/caddy-dns/powerdns)
- **PowerDNS:** [PowerDNS/pdns](https://github.com/PowerDNS/pdns)

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
