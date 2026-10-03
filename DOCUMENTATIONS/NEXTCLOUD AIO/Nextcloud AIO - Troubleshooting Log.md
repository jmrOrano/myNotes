*Part of the Nextcloud AIO notes. See also: [[Nextcloud AIO - Overview & Basic Setup]], [[Nextcloud AIO - Cloudflare Domain Setup]], [[Nextcloud AIO - Cloudflare + NPM]], [[Nextcloud AIO - Cloudflared Tunnel]], [[Nextcloud AIO - Fail2ban & Security]]*

>[!Contents]
>- [[#Grant Access Loop with the dedicated app.]]
>- [[#Download Stopping at 1.2Gb]]

---

## **Problems Encountered.**
---
##### G**rant Access Loop with the dedicated app.** 
*Overview: Uploading via web browser is slower, so i decided to install the desktop app itself as i read that it is faster.*
Procedure to recreate or for documentation. 
- Install the desktop app
- Login 
- Redirect to browser
- Grant access --this is where the loop issue is. 

###### **First Attemp**:
Using this Solution as reference: ** https://help.nextcloud.com/t/macos-and-ios-clients-stuck-in-grant-access-loop/52279/3

Verifying the `config/php` if it has the entries
```
overwrite.cli.url
overwriteprotocol
trusted_proxies
```

I run:
```Bash
docker exec -it nextcloud-aio-nextcloud

#Then read the config located at /var/www/html/config/config.php
cat -n /var/www/html/config/config.php | grep "overwrite.cli.url" #and the overwriteprotocol, trusted_proxies
```

Upon checking, it there: 
```
`overwrite.cli.url => 'https://cloud.orano.lab.com/' #which is my bought domain from cloudflare 
`overwriteprotocol` => `https` 
`trusted_proxies` => 
array (
	0 => '127.0.0.1',
	1 => '::1',
	10 => '172.20.0.0/16', ),
```
Therefore, skipping this. 

###### **Second attemp**

I check read the logs:
```Bash
docker exec -it nextcloud-aio-nextcloud bash

#Then
tail -f /var/www/html/data/nextcloud.log

#Output
e":"Login failed: admin (Remote IP: 172.71.214.221)","userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:151.0) Gecko/20100101 Firefox/151.0","version":"33.0.3.2","data":[]}
{"reqId":"f7JmuZVfa0DkLk8nfBbZ","level":2,"time":"2026-05-30T13:58:17+00:00","remoteAddr":"172.71.214.221","user":"--","app":"no app in context","method":"POST","url":"/login","scriptName":"/index.php","message":"Login failed: admin (Remote IP: 172.71.214.221)","userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:151.0) Gecko/20100101 Firefox/151.0","version":"33.0.3.2","data":[]}****
```

Hell, it turns out the password is just wrong. The browser auto completes the wrong password.

----

##### Download Stopping at 1.2Gb
*Overview: Downloading a video file size of 2.4Gb into a phone via the app. Somehow, its stopping at 53%*

Tried the ff:
- [x] Check for logs.
	Nothing unusual, no error
- [x] Turned of the proxy mode in cloudflare 
	*Reason: could be due to free limit of 100Mb per request in cloudflare. Didnt fix it*
- [x] Suspected Php config
	*Reason: it could be to upload_max_filesize, post_max_size, memory_limit*. Didnt fix it. The value is already high enough.
- [x] Adding an NPM Configuration
	*Reason: could be due to NPM pausing the connection mid transfer* Didnt fix it.
```
	client_max_body_size 0;
	proxy_request_buffering off;
	proxy_buffering off;
	proxy_connect_timeout 3600;
	proxy_send_timeout 3600;
	proxy_read_timeout 3600;
	send_timeout 3600;
	proxy_max_temp_file_size 0;
	keepalive_timeout 3600;
	keepalive_requests 100;
	#proxy_http_version 1.1;
	proxy_set_header Connection "";
```
- [x] Tested downloading via Browser (Web UI in Laptop)
	*Reason: just to eliminate some hunch* (It downloaded without issue)
	A clue arise: it could be just the mobile app having an issue.
- [x] Setup and use a different platform
	*Use EX File explorer, setup a WebDav by copying the WebDav Link in > Files>Setting>WebDav*
	and pasted in the App and did a download.
	It worked.
	It turns out, its the mobile platform having an issue.  Though the main cause is still unknown.
