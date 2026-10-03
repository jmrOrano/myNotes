*Part of the Nextcloud AIO notes. See also: [[Nextcloud AIO - Overview & Basic Setup]], [[Nextcloud AIO - Cloudflare Domain Setup]], [[Nextcloud AIO - Cloudflare + NPM]], [[Nextcloud AIO - Fail2ban & Security]], [[Nextcloud AIO - Troubleshooting Log]]*

>[!Contents]
>- [[#==Step 1: Verify the current state of the Docker==]]
>- [[#==Step 2: Change the compose file==]]
>- [[#==Step 3: Add nextcloud ingress rule in config.yml==]]
>- [[#==Step 4: Route the DNS to the subdomain==]]
>- [[#==Step 5: Verify if the new ingress rule is applied==]]
>- [[#==Step 6: (If migrating from NPM) Retire the NPM==]]

---

#### ==**Cloudfalred tunnel approach**==
In case a network provided by an ISP is behind a CGNAT. 

##### ==Step 1: Verify the current state of the Docker==
```
docker ps -a
```

##### ==Step 2: Change the compose file==
Comment the port 80:80 and port 8443:8443, set `APACHE_PORT: 11000`.
Similar approach to what the compose file of  [[Nextcloud AIO - Cloudflare + NPM#**USING CLOUDFLARE + NGINX PROXY MANAGER**]]

**Note:** if migrating from an existing NPM setup, this is already done — just verify via `cat compose.yaml | grep -A 20 "ports:"`, no need to redo.

Restart the mastercontainer if necessary. 

##### ==Step 3: Add nextcloud ingress rule in config.yml==

For cloudflared setup. Refer to: [[SSH_Setup#Cloudflare tunnel]]

Config.yml
```nano
tunnel: <tunnelID>
credentials-file: /home/<user>/.cloudflared/<TUNNEL_ID>

ingress:
  - hostname: <subdomain>.<yourdomain>.com
    service: http://localhost:11000
  - service: http_status:404

```
the `- service: http_status:404` should always be at end if have multiple service

##### ==Step 4: Route the DNS to the subdomain==
```
cloudflared tunnel route dns homelab_tunnel <yoursubdomain>.<yourdomain>.com
```
A message should appear:
```
INF Added CNAME <yoursubdomain>.<yourdomain>.com which will route to this tunnel tunnelID=<tunnelID>
```

##### ==Step 5: Verify if the new ingress rule is applied==
```
sudo systemctl status cloudflared
```

##### ==Step 6: (If migrating from NPM) Retire the NPM==
Verify Cloudflare Tunnel is truly serving the traffic — not NPM — before retiring it:
```
docker stop nginx-proxy-manager
```
Reload/access `https://cloud.yourdomain.com` again. If still accessible, Cloudflare Tunnel is confirmed as the actual route.
Keep NPM stopped (not removed) for a few days as fallback before fully retiring it (`docker compose down` / `docker rm`).
