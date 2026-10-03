*Part of the Nextcloud AIO notes. See also: [[Nextcloud AIO - Cloudflare Domain Setup]], [[Nextcloud AIO - Cloudflare + NPM]], [[Nextcloud AIO - Cloudflared Tunnel]], [[Nextcloud AIO - Fail2ban & Security]], [[Nextcloud AIO - Troubleshooting Log]]*

*Purpose: I plan to host a cloud storage so instead of paying for plan, I pay for physical storage.*
Date: May 02, 2026 -
```
Overview 1:

Machine and OS : Linux Mint 22.3 Zena, Ubuntu Base Noble 24.04
Kernel : 6.17.0-23-generic
Documentataion I followed: https://github.com/nextcloud/all-in-one#nextcloud-all-in-one

Overview 2: The tradeoff with this method.

**What you will gain**
✔ Easy deployment
✔ Automatic updates
✔ Working stack out of the box

**What you lost**
❌ Full control
❌ Transparency
❌ Deep understanding (for now)


```

>[!Contents]
>My Procedure.
>- [[#**INSTALL DOCKER**]]
>- [[#**GET THE COMPOSE FILE FROM THE GITHUB REPO**]]
>- [[#**MAKE A DIRECTORY AND EXECUTE THE FILE**]]
>- [[#**CHECK LOGS AND CHECK THINGS**]]
>- [[#**ASSIGN A DNS FOR IP (duckdns.org)**]]
>
>Other Information
>- [[Nextcloud AIO - Cloudflare + NPM#Allow nextcloud container to access directories on the host.]]
>- [[SSH_Setup#**REVERSE PROXY**|Understanding Reverse Proxy]]

---

#### **INSTALL DOCKER**
- Follow this guide [[DOCKER SETUP  2| How I set up my docker]
>[!Warning]
>Snap-based docker installations are not supported.
>To check, run this:
>```Bash
>sudo docker info | grep "Docker Root Dir" | grep "/var/snap/docker/"
>```

#### **GET THE COMPOSE FILE FROM THE GITHUB REPO**
LINK : `https://github.com/nextcloud/all-in-one/blob/main/compose.yaml`

###### Or i can just put the entire thing here
```Bash
name: nextcloud-aio # Add the container to the same compose project like all the sibling containers are added to automatically.
services:
  nextcloud-aio-mastercontainer:
    image: ghcr.io/nextcloud-releases/all-in-one:latest # This is the container image used. You can switch to ghcr.io/nextcloud-releases/all-in-one:beta if you want to help testing new releases. See https://github.com/nextcloud/all-in-one#how-to-switch-the-channel
    init: true # This setting makes sure that signals from main process inside the container are correctly forwarded to children. See https://docs.docker.com/reference/compose-file/services/#init
    restart: always # This makes sure that the container starts always together with the host OS. See https://docs.docker.com/reference/compose-file/services/#restart
    container_name: nextcloud-aio-mastercontainer # This line is not allowed to be changed as otherwise AIO will not work correctly
    volumes:
      - nextcloud_aio_mastercontainer:/mnt/docker-aio-config # This line is not allowed to be changed as otherwise the built-in backup solution will not work
      - /var/run/docker.sock:/var/run/docker.sock:ro # May be changed on macOS, Windows or docker rootless. See the applicable documentation. If adjusting, don't forget to also set 'WATCHTOWER_DOCKER_SOCKET_PATH'!
    # devices: ["/dev/dri"] # Uncomment to enable hardware acceleration. ⚠️⚠️⚠️ Warning: this only works if the '/dev/dri' device is present on the host! If it should not exist on your host, don't add this as otherwise the mastercontainer will fail to start! See https://github.com/nextcloud/all-in-one#how-to-enable-hardware-acceleration-for-nextcloud
    network_mode: bridge # This adds the container to the same network as docker run would do. Comment this line and uncomment the line below and the networks section at the end of the file if you want to define a custom MTU size for the docker network
    # networks: ["nextcloud-aio"]
    ports:
      - "80:80" # Can be removed when running behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      - "8080:8080" # This is the AIO interface, served via https and self-signed certificate. See https://github.com/nextcloud/all-in-one#explanation-of-used-ports
      - "8443:8443" # Can be removed when running behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
    # security_opt: ["label:disable"] # Is needed when using SELinux. See https://github.com/nextcloud/all-in-one#are-there-known-problems-when-selinux-is-enabled
    # environment: # Is needed when using any of the options below
      # AIO_DISABLE_BACKUP_SECTION: false # Setting this to true allows to hide the backup section in the AIO interface. See https://github.com/nextcloud/all-in-one#how-to-disable-the-backup-section
      # APACHE_PORT: 11000 # Is needed when running behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      # APACHE_IP_BINDING: 127.0.0.1 # Should be set when running behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else) that is running on the same host. See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      # APACHE_ADDITIONAL_NETWORK: frontend_net # (Optional) Connect the apache container to an additional docker network. Needed when behind a web server or reverse proxy (like Apache, Nginx, Caddy, Cloudflare Tunnel and else) running in a different docker network on same server. See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      # BORG_RETENTION_POLICY: --keep-within=7d --keep-weekly=4 --keep-monthly=6 # Allows to adjust borgs retention policy. See https://github.com/nextcloud/all-in-one#how-to-adjust-borgs-retention-policy
      # AIO_LOG_LEVEL: warn # Allows to globally adjust the log level of the included AIO components. Supported values: debug, info, warn, error. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-log-level-for-aio-components
      # COLLABORA_SECCOMP_DISABLED: false # Setting this to true allows to disable Collabora's Seccomp feature. See https://github.com/nextcloud/all-in-one#how-to-disable-collaboras-seccomp-feature
      # DOCKER_API_VERSION: 1.44 # You can adjust the internally used docker api version with this variable. ⚠️⚠️⚠️ Warning: please note that only the default api version (unset this variable) is supported and tested by the maintainers of Nextcloud AIO. So use this on your own risk and things might break without warning. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-internally-used-docker-api-version
      # FULLTEXTSEARCH_JAVA_OPTIONS: "-Xms1024M -Xmx1024M" # Allows to adjust the fulltextsearch java options. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-fulltextsearch-java-options
      # NEXTCLOUD_DATADIR: /mnt/ncdata # Allows to set the host directory for Nextcloud's datadir. ⚠️⚠️⚠️ Warning: do not set or adjust this value after the initial Nextcloud installation is done! See https://github.com/nextcloud/all-in-one#how-to-change-the-default-location-of-nextclouds-datadir
      # NEXTCLOUD_MOUNT: /mnt/ # Allows the Nextcloud container to access the chosen directory on the host. See https://github.com/nextcloud/all-in-one#how-to-allow-the-nextcloud-container-to-access-directories-on-the-host
      # NEXTCLOUD_UPLOAD_LIMIT: 16G # Can be adjusted if you need more. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-upload-limit-for-nextcloud
      # NEXTCLOUD_MAX_TIME: 3600 # Can be adjusted if you need more. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-max-execution-time-for-nextcloud
      # NEXTCLOUD_MEMORY_LIMIT: 512M # Can be adjusted if you need more. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-php-memory-limit-for-nextcloud
      # NEXTCLOUD_TRUSTED_CACERTS_DIR: /path/to/my/cacerts # CA certificates in this directory will be trusted by the OS of the nextcloud container (Useful e.g. for LDAPS) See https://github.com/nextcloud/all-in-one#how-to-trust-user-defined-certification-authorities-ca
      # NEXTCLOUD_STARTUP_APPS: deck twofactor_totp tasks calendar contacts notes # Allows to modify the Nextcloud apps that are installed on starting AIO the first time. See https://github.com/nextcloud/all-in-one#how-to-change-the-nextcloud-apps-that-are-installed-on-the-first-startup
      # NEXTCLOUD_ADDITIONAL_APKS: imagemagick # This allows to add additional packages to the Nextcloud container permanently. Default is imagemagick but can be overwritten by modifying this value. See https://github.com/nextcloud/all-in-one#how-to-add-os-packages-permanently-to-the-nextcloud-container
      # NEXTCLOUD_ADDITIONAL_PHP_EXTENSIONS: imagick # This allows to add additional php extensions to the Nextcloud container permanently. Default is imagick but can be overwritten by modifying this value. See https://github.com/nextcloud/all-in-one#how-to-add-php-extensions-permanently-to-the-nextcloud-container
      # NEXTCLOUD_ENABLE_NVIDIA_GPU: true # This allows to enable the NVIDIA runtime and GPU access for containers that profit from it. ⚠️⚠️⚠️ Warning: this only works if an NVIDIA gpu is installed on the server. See https://github.com/nextcloud/all-in-one#how-to-enable-hardware-acceleration-for-nextcloud.
      # NEXTCLOUD_KEEP_DISABLED_APPS: false # Setting this to true will keep Nextcloud apps that are disabled in the AIO interface and not uninstall them if they should be installed. See https://github.com/nextcloud/all-in-one#how-to-keep-disabled-apps
      # SKIP_DOMAIN_VALIDATION: false # This should only be set to true if things are correctly configured. See https://github.com/nextcloud/all-in-one#how-to-skip-the-domain-validation
      # TALK_PORT: 3478 # This allows to adjust the port that the talk container is using which is exposed on the host. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-talk-port
      # WATCHTOWER_DOCKER_SOCKET_PATH: /var/run/docker.sock # Needs to be specified if the docker socket on the host is not located in the default '/var/run/docker.sock'. Otherwise mastercontainer updates will fail. For macos it needs to be '/var/run/docker.sock'

#   # Optional: Caddy reverse proxy. See https://github.com/nextcloud/all-in-one/discussions/575
#   # Alternatively, use Tailscale if you don't have a domain yet. See https://github.com/nextcloud/all-in-one/discussions/6817
#   # Hint: You need to uncomment APACHE_PORT: 11000 above, adjust cloud.example.com to your domain and uncomment the necessary docker volumes at the bottom of this file in order to make it work
#   # You can find further examples here: https://github.com/nextcloud/all-in-one/discussions/588
#   caddy:
#     image: caddy:alpine
#     restart: always
#     container_name: caddy
#     volumes:
#       - caddy_certs:/certs
#       - caddy_config:/config
#       - caddy_data:/data
#       - caddy_sites:/srv
#     network_mode: "host"
#     configs:
#       - source: Caddyfile
#         target: /etc/caddy/Caddyfile
# configs:
#   Caddyfile:
#     content: |
#       # Adjust cloud.example.com to your domain below
#       https://cloud.example.com:443 {
#         reverse_proxy localhost:11000
#       }

volumes: # If you want to store the data on a different drive, see https://github.com/nextcloud/all-in-one#how-to-store-the-filesinstallation-on-a-separate-drive
  nextcloud_aio_mastercontainer:
    name: nextcloud_aio_mastercontainer # This line is not allowed to be changed as otherwise the built-in backup solution will not work
  # caddy_certs:
  # caddy_config:
  # caddy_data:
  # caddy_sites:

# # Adjust the MTU size of the docker network. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-mtu-size-of-the-docker-network
# networks:
#   nextcloud-aio:
#     name: nextcloud-aio
#     driver_opts:
#       com.docker.network.driver.mtu: 1440
```

#### **MAKE A DIRECTORY AND EXECUTE THE FILE**
```Bash
mkdir ~/containers/nextcloud-aio-mastercontainer #I just used the container name as the dir name to avoid confusion
#put the compose.yaml inside and run:
docker compose -f compose.yaml up -d
```

#### **CHECK LOGS AND CHECK THINGS**
```Bash
docker logs -f nextcloud-aio-mastercontainer
docker ps
```

You should see a message in logs something like:
```
Trying to fix docker.sock permissions internally...
Creating docker group internally with id 984
Initial startup of Nextcloud All-in-One complete!
You should be able to open the Nextcloud AIO Interface now on port 8080 of this server!
E.g. https://internal.ip.of.this.server:8080
⚠️ Important: do always use an ip-address if you access this port and not a domain as HSTS might block access to it later!

If your server has port 80 and 8443 open and you point a domain to your server, you can get a valid certificate automatically by opening the Nextcloud AIO Interface via:
https://your-domain-that-points-to-this-server.tld:8443
[02-May-2026 07:02:39] NOTICE: fpm is running, pid 159

```

#### **ASSIGN A DNS FOR IP (duckdns.org)**
Follow first the instruction according to the logs.  It will tell you to login via your private ip for now with port 8080.Save your pass phrase.

- An option for assigning your Ip in domain is either buying a domain and setting it up in cloudflare.
- But in this documentation,  choosing a ddns is also an option. duckdns.org it is, then choose a dnsname.
	- Go to duckdns.org
	- Map your public to desired ddns.
- The, go to routers dashboard and port forward the port 443 to internal machine private IP.
- Back to Nextcloud dashboard: Enter the dnsname you set to your ip in duckdns and enter.
- From here on, just follow things. There are hyperlinks leads to github if curious about things.
- After that. Several Containers will intall more such as the :
	- **Apache**
		- *the front door. Every web request from your browser hits this first. It handles HTTPS (via Let's Encrypt), then forwards traffic internally to Nextcloud. This is also the one that becomes relevant when you put a reverse proxy in front of it.*
	- **PostgreSQL**
		- *he database. Stores everything _about_ your files — user accounts, sharing permissions, metadata, app settings, folder structure. Not the files themselves.*
	- **Nextcloud**
		- *the actual application. The PHP backend that runs the cloud logic — file management, user auth, app framework, etc. Everything else exists to support this one.*
	- **Client Push**
		- *enables real-time notifications. Without it, your desktop/mobile client polls the server repeatedly asking "anything new?" With it, the server pushes updates instantly. Better for performance.*
	- **Redis**
		- *short-term memory. Handles caching and session data so Nextcloud doesn't have to hit PostgreSQL for every single request. Makes things faster and handles file locking (prevents two people corrupting the same file simultaneously).*
	- **Nextcloud Office**
		- *the Collabora-based engine that lets you open and edit `.docx`, `.xlsx`, `.odt` files directly in the browser, like a self-hosted Google Docs.*
	- **NextCloud Talk**
		- *video calls, chat, and messaging. Its own mini-communication platform built into Nextcloud.*
	- **Nextcloud Whiteboard**
		- *collaborative whiteboard feature, the newest of the bunch. Think Miro but self-hosted.*
	Normally. All of them youd set them up one by one. But with AIO it just mass-deploy everything.
	
- After that, the initial credentias will display. use that to login. NOW THIS IS WHERE THINGS JUST GETTING STARTED.


>[!Note]
>In case you as admin forgot your own password with no SMPT, but have the access to server as root. Do this
>```Bash
>docker ps # to check the stataus
>docker exec -it nextcloud-aio-nextcloud bash
>sudo -E -u www-data php occ user:resetpassword <username>
>```
>
>>[!Question] Where is the data located(such as the files, pictures, vidoes etc....)
>>*Its located at host machine:  `/var/lib/docker/volumes/nextcloud_aio_nextcloud_data/_data/`*
>


>[!Note]- Summary of this whole thing
>**DOCKER LAYER  (Foundation)**
>- setting up the docker
>- learning to read github repositories
>- using compose file and executing using `docker compose up -d`
>- Resulted in : nextcloud-aio-mastercontainer
>	- This container is a **controller**, not the cloud server itself.
>	- It uses: `/var/run/docker,sock`\ to spawn the following containers.
>		- Nextcloud 
>		-  Database
>		- Redis
>		- Apache
>		- etc.
>
>**AIO Architecture ( what actually runs)**
>```
>MASTER CONTAINER
>     |
>[ APACHE ], [NEXTCLOUD], [POSTGRESQL], [REDIS], ETC...
>```
>
>**ACCESS POINT**
>-`Before Installation`
>- The initial setup can be access  via localhost at port 8080.
>- AIO Setup, Not the final app yet.
>- Having a domain name is required for the setup
>
>`After Installation in the AIO setup`
>- The mentioned containers in AIO Architecture running.
>- Apache handles the Auto SSL,TLS, auto HTTPS (via lets Encrypt), reverse proxy, routing to internal containerss, handle nextcloud specific behavior)
>
>**UNDERSTANDING USING DDNS VS REAL DOMAIN** (for future reference)
>`DNS layer (DucksDNS)`
>- free
>- basic 
>- no extra features
>- 
>  `Real Domain`
>- Same concept but with more features, like with cloudflare : basic ddos protection, ssl automation, proxying , dns hosting.
