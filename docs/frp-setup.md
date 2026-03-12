# FRP Tunnel Setup

If you want to expose your local Lobster Kingdom instance to the internet, you can use FRP (Fast Reverse Proxy).

## Prerequisites

- A server with a public IP
- FRP server (frps) running on the server
- FRP client (frpc) on your local machine

## Server Setup (frps)

1. Download FRP: https://github.com/fatedier/frp/releases

2. Create `frps.ini`:

```ini
[common]
bind_port = 7000
vhost_http_port = 80
subdomain_host = your-domain.com
```

3. Start frps:

```bash
./frps -c frps.ini
```

## Client Setup (frpc)

1. Download FRP client

2. Create `frpc.ini`:

```ini
[common]
server_addr = your-server-ip
server_port = 7000

[lobster-kingdom]
type = http
local_port = 3995
subdomain = lobster
```

3. Start frpc:

```bash
./frpc -c frpc.ini
```

4. Access your app at: `http://lobster.your-domain.com`

## Security Considerations

- Use HTTPS (add SSL certificate to your server)
- Enable authentication in FRP
- Use firewall rules to restrict access
- Consider rate limiting at the server level

## Alternative: ngrok

For quick testing, you can use ngrok:

```bash
ngrok http 3995
```

This will give you a temporary public URL.

## Alternative: Cloudflare Tunnel

Cloudflare Tunnel (formerly Argo Tunnel) is another option:

```bash
cloudflared tunnel --url http://localhost:3995
```

More secure and doesn't require opening ports on your firewall.
