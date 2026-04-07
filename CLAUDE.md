# UniFi Cloudflare DDNS — Claude Context

## Project Overview
Fork of a Cloudflare Worker that provides a UniFi-compatible DDNS API endpoint.
When your home IP changes, UniFi Dream Machine (or USG) calls this Worker,
which then updates the Cloudflare DNS A record for your domain.

## Fork Notes
- Upstream: `https://github.com/workerforce/unifi-ddns` (or similar)
- Any local customizations should be documented here as they're made

## Tech Stack
- **Cloudflare Workers** — JavaScript (`src/index.js`)
- **Wrangler CLI** — deployment tool
- Worker name: `unifi-cloudflare-ddns`

## Deployment
```bash
wrangler deploy
```
Worker is deployed to `unifi-cloudflare-ddns.<subdomain>.workers.dev`

## How It Works
1. UniFi device sends a DDNS update request to the Worker URL (with IP + hostname)
2. Worker authenticates using a Cloudflare API token (passed as password by UniFi)
3. Worker calls Cloudflare API to update the DNS A record for the specified hostname

## UniFi Configuration
In UniFi OS → Settings → Internet → WAN → Dynamic DNS:
- **Service:** `custom` or `dyndns`
- **Hostname:** full domain to update (e.g. `home.mydomain.com`)
- **Username:** domain zone (e.g. `mydomain.com`)
- **Password:** Cloudflare API token (with "Edit zone DNS" permission)
- **Server:** `<worker-name>.<subdomain>.workers.dev/update?ip=%i&hostname=%h` (no `https://`)

## Cloudflare API Token Setup
Create at https://dash.cloudflare.com/profile/api-tokens using the "Edit zone DNS" template.
Scope it to the specific zone being updated.

## Testing
On UDM-Pro (SSH):
```bash
inadyn -n -1 --force -f /run/ddns-eth4-inadyn.conf
```
On USG (SSH):
```bash
sudo ddclient -daemon=0 -verbose -noquiet -debug -file /etc/ddclient/ddclient_eth0.conf
```
