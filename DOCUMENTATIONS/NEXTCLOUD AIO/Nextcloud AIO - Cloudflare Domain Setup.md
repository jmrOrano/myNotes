*Part of the Nextcloud AIO notes. See also: [[Nextcloud AIO - Overview & Basic Setup]], [[Nextcloud AIO - Cloudflare + NPM]], [[Nextcloud AIO - Cloudflared Tunnel]], [[Nextcloud AIO - Fail2ban & Security]], [[Nextcloud AIO - Troubleshooting Log]]*

>[!Contents]
>- [[#**USING A BOUGHT DOMAIN and DOMAIN HOSTING PLATFORM (CLOUDFLARE)**]]

---

#### **USING A BOUGHT DOMAIN and DOMAIN HOSTING PLATFORM (CLOUDFLARE)**
*Overview: The procedure above is a straightforward setup that uses the apache container provided in AIO for its auto HTTPS via lets encrypt and acts as an inner reverse proxy.  With cloudflare it offers features like proxy, auto tls/ssl*

I bought a domain directly at cloudflare as i ve read its pricing is stable.
Login to cloudflare and access the  : 
	`Domains` -> `Overview` -> `clickyourDomainName` -> `Records` 

Add an `A Record` for how will the subdomain will be:
	Under records: `Click Add Record`
		Type : `A`
		Name: `yourdesiredName` `my domain is oranlab.com so when i put cloud as a name, it becomes cloud.oranolab.com`
		IPv4 addr: `<yourPublicIP>`
		Proxy status: `cloud grayed` `#for temporary only`
	
>[!Important] Reminder when it comes to changing domain 
>During the setup of new AIO stance wherein you need to enter a domain. Currently , there is no way to change this domain afterwards from AIO interface. So if you have set things up already in domain like the duckdns as the [[Nextcloud AIO - Overview & Basic Setup#**ASSIGN A DNS FOR IP (duckdns.org)**|above procedure]]. The ways to change it are:
>- Changing the json file manually by running the command
>```Bash
>`sudo docker run -it --rm --volume nextcloud_aio_mastercontainer:/mnt/docker-aio-config:rw alpine sh -c "apk add --no-cache nano && nano /mnt/docker-aio-config/data/configuration.json"`
>```
>- Full reset (Recommended). By removing all the containers and volume. Though it will remove users file and data as well
>	[[Docker-Reference#Images]]

>[!Important] Reminder in using clouflare as domain hosting platform.
> - Disable the proxy in `proxy status` during the adding `A record`. And also disable the `Custom SSL/TLS` under the `yourdomainname` -> `SSL/TLS` ->`Overview` or else there will be an error message during the enering domain step in AIO interface. Like: 
> ```Bash
> Domain does not point to this server or the reverse proxy is not configured correctly. See the mastercontainer logs for more details. ('sudo docker logs -f nextcloud-aio-mastercontainer') If you should be using Cloudflare, make sure to disable the Cloudflare Proxy feature as it might block the domain validation. Same for any other firewall or service that blocks unencrypted access on port 443.
> ```
> - I need to do search about it what causes that error message. If i can replicate it, then i ill understand it.
> 
> You can enable it back once the you input the domain in the AIO interface and manage to install the other containers.
> Once thats done you can proceed to enter the domain in AIO interface domain validation and Install the listed containers.

---

##### Cloudflare free-plan upload limit
Cloudflare only has 100mb file upload
	see this for more: https://github.com/nextcloud/all-in-one#notes-on-cloudflare-proxytunnel

	```Cloudflare only supports uploading files up to 100 MB in the free plan, if you try to upload bigger files you will get an error (413 - Payload Too Large) if no chunking is used (e.g. for public uploads in the web, or if chunks are configured to be bigger than 100 MB in the clients or the web). If you need to upload bigger files, you need to disable the proxy option in your DNS settings. Note that this will both disable Cloudflare DDoS protection and Cloudflare Tunnel as these services require the proxy option to be enabled.```
