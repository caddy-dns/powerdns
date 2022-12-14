PowerDNS module for Caddy
=========================

This package contains a DNS provider module for [Caddy](https://github.com/caddyserver/caddy). It can be used to manage DNS records with a PowerDNS.

## Caddy module name

```
dns.providers.powerdns
```

## Config examples

To use this module for the ACME DNS challenge, [configure the ACME issuer in your Caddy JSON](https://caddyserver.com/docs/json/apps/tls/automation/policies/issuers/acme/) like so:

```json
{
	"module": "acme",
	"challenges": {
		"dns": {
			"provider": {
				"name": "powerdns",
				"server_url": "{env.POWERDNS_SERVER_URL}",
				"api_token": "{env.POWERDNS_API_TOKEN}"
			}
		}
	}
}
```

or with the Caddyfile:

```
tls {
	dns powerdns {env.POWERDNS_SERVER_URL} {env.POWERDNS_API_TOKEN}
}
```

You can replace `{env.POWERDNS_SERVER_URL}` and `{env.POWERDNS_API_TOKEN}` with the actual server URL and API token if you prefer to put it directly in your config instead of an environment variable.
