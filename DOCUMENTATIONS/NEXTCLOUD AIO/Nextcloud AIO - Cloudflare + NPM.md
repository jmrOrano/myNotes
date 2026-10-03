*Part of the Nextcloud AIO notes. See also: [[Nextcloud AIO - Overview & Basic Setup]], [[Nextcloud AIO - Cloudflare Domain Setup]], [[Nextcloud AIO - Cloudflared Tunnel]], [[Nextcloud AIO - Fail2ban & Security]], [[Nextcloud AIO - Troubleshooting Log]]*

>[!Contents]
>- [[#**USING CLOUDFLARE + NGINX PROXY MANAGER**]]
>- [[#Allow nextcloud container to access directories on the host.]]

---

#### **USING CLOUDFLARE + NGINX PROXY MANAGER**
*Recommended to do a full reset first. see the [[Docker-Reference#Images]]

**Change the port in compose file of the  mastercontainer**
From:
```Bash
   ports:
      - "80:80" # Can be removed when running behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      - "8080:8080" # This is the AIO interface, served via https and self-signed certificate. See https://github.com/nextcloud/all-in-one#explanation-of-used-ports
      - "8443:8443" # Can be removed when running behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
    # security_opt: ["label:disable"] # Is needed when using SELinux. See https://github.com/nextcloud/all-in-one#are-there-known-problems-when-selinux-is-enabled
    # environment: # Is needed when using any of the options below
      # AIO_DISABLE_BACKUP_SECTION: false # Setting this to true allows to hide the backup section in the AIO interface. See https://github.com/nextcloud/all-in-one#how-to-disable-the-backup-section
      # APACHE_PORT: 11000
```

To:
```Bash
   ports:
      #- "80:80" # Can be removed when running behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      - "8080:8080" # This is the AIO interface, served via https and self-signed certificate. See https://github.com/nextcloud/all-in-one#explanation-of-used-ports
      #- "8443:8443" # Can be removed when running behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
    # security_opt: ["label:disable"] # Is needed when using SELinux. See https://github.com/nextcloud/all-in-one#are-there-known-problems-when-selinux-is-enabled
	environment: # Is needed when using any of the options below
      # AIO_DISABLE_BACKUP_SECTION: false # Setting this to true allows to hide the backup section in the AIO interface. See https://github.com/nextcloud/all-in-one#how-to-disable-the-backup-section
       APACHE_PORT: 11000
```
>[!Question] Why comment those port? 
>*Because in this setup, the nginx proxy manager will be using those port. That thing will be the one receiving the request from cloudflare to to redirect it inside. Only port number for the AIO interface will be left.*

**Install nginx proxy manager via compose**
```Bash            
services:
  
  nginx-proxy-manager:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    container_name: nginx-proxy-manager
    ports: # These ports are in format <host-port>:<container-port>
      - '80:80'
      - '443:443'
      - '81:81'
        #add any other stream port you want to expose
      # - '21:21' FTP
 
    # environment: #uncomment if ipv is DISABLED  in host
     # - DISABLE_IPV6=true

    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```

**Run the nginx compose and the aio**
```Bash
docker compose -f ~/<path>nginx-compose.yaml up -d
docker compose -f ~/<path>nextclouid-aio-mastercontainer.yaml up -d
```
**Setup a subdomain in cloudflare**
Login to cloudflare and access the  : 
	`Domains` -> `Overview` -> `clickyourDomainName` -> `Records` 

Add an `A Record` for how will the subdomain will be:
	Under records: `Click Add Record`
		Type : `A`
		Name: `yourdesiredName` `my domain is oranlab.com so when i put cloud as a name, it becomes cloud.oranolab.com`
		IPv4 addr: `<yourPublicIP>`
		Proxy status: `cloud grayed` `#for temporary only`

**Generate an ssl certificate incloudflare**
>[!Note] 
>In this method, it only uses the cloudflare ssl certificate itself with no generating API token for the letsEncrypt usage.

- Navigate to : `SSL/TLS` -> `origin server` and generate cert
- Copy both the `Origin Cert` and the `Private key` 
- Save them as `.pem` file  as seperate file

**Login to Nginx proxy manger**
```
http://<yourlocalip>:81 #as stated in compose file earlier
```

**Add SSL Certificate in NPM**
- Click add ssl certificate and choose `Custom` 
- Upload the private key file you save to the `Cert key`
- Upload the Origin key file you saved to the `Cert`

**Add a proxy host in NPM**
```
Domain name: whatyousetupincloudflare.yourdomain.com
Scheme: http 
Forwarded Hostname /IP : your machine local ip
Port: 11000 # as stated in compose file earlier
Check the : Block Common exploits and Websocket support

ssl section.
Choose the saved cloudflare cert
Force ssl
HTTP2 Support

```
Save

**Login in Nexcloud AIO interface**
- Access via your local machine ip port 8080
- Login via passphrase
- Type the the domain name
- At this point, it should be straighforward now.

#### Allow nextcloud container to access directories on the host. 
*Overview: Follow READ this : https://github.com/nextcloud/all-in-one#how-to-store-the-filesinstallation-on-a-separate-drive*

Or i can just put it here: 
>[!Quote]-
>By default, the Nextcloud container is confined and cannot access directories on the host OS. You might want to change this when you are planning to use local external storage in Nextcloud to store some files outside the data directory and can do so by adding the environmental variable `NEXTCLOUD_MOUNT` to the docker run command of the mastercontainer (but before the last line `ghcr.io/nextcloud-releases/all-in-one:latest`! If it was started already, you will need to stop the mastercontainer, remove it (no data will be lost) and recreate it using the docker run command that you initially used). Allowed values for that variable are strings that start with `/` and are not equal to `/`.
>
>- Two examples for Linux are `--env NEXTCLOUD_MOUNT="/mnt/"` and `--env NEXTCLOUD_MOUNT="/media/"`.
>- On macOS it might be `--env NEXTCLOUD_MOUNT="/Volumes/your_drive/"`
>- For Synology it may be `--env NEXTCLOUD_MOUNT="/volume1/"`.
>- On Windows it might be `--env NEXTCLOUD_MOUNT="/run/desktop/mnt/host/d/your-folder/"`. (This path is equivalent to `D:\your-folder` on your Windows host so you need to translate the path accordingly. Hint: the path that you enter needs to start with `/run/desktop/mnt/host/`. Append to that the exact location on your windows host, e.g. `d/your-folder/` which is equivalent to `D:\your-folder`.) ⚠️ **Please note**: This does not work with external drives like USB or network drives and only with internal drives like SATA or NVME drives.
>
After using this option, please make sure to apply the correct permissions to the directories that you want to use in Nextcloud. E.g. `sudo chown -R 33:0 /mnt/your-drive-mountpoint` and `sudo chmod -R 750 /mnt/your-drive-mountpoint` should make it work on Linux when you have used `--env NEXTCLOUD_MOUNT="/mnt/"`. On Windows you could do this e.g. with `docker exec -it nextcloud-aio-nextcloud chown -R 33:0 /run/desktop/mnt/host/d/your-folder/` and `docker exec -it nextcloud-aio-nextcloud chmod -R 750 /run/desktop/mnt/host/d/your-folder/`.
>
You can then navigate to `https://your-nc-domain.com/settings/apps/disabled`, activate the external storage app, navigate to `https://your-nc-domain.com/settings/admin/externalstorages` and add a local external storage directory that will be accessible inside the container at the same place that you've entered. E.g. `/mnt/your-drive-mountpoint` will be mounted to `/mnt/your-drive-mountpoint` inside the container, etc.
>
Be aware though that these locations will not be covered by the built-in backup solution - but you can add further Docker volumes and host paths that you want to back up after the initial backup is done.


Reveres Proxy
DNS
SSL Certificate
