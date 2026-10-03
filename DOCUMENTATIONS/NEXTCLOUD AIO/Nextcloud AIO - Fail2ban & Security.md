*Part of the Nextcloud AIO notes. See also: [[Nextcloud AIO - Overview & Basic Setup]], [[Nextcloud AIO - Cloudflare Domain Setup]], [[Nextcloud AIO - Cloudflare + NPM]], [[Nextcloud AIO - Cloudflared Tunnel]], [[Nextcloud AIO - Troubleshooting Log]]*

>[!Contents]
>- [[#**Fail2ban implementation**]]
>- [[#Fail2ban limitation]]
>- [[#Where to find the logs]]
>- [[#Fail2ban banning but Brute forcing still possible]]

---

#### **Fail2ban implementation**
---
Follow the documentation at: https://docs.nextcloud.com/server/stable/admin_manual/installation/harden_server.html#setup-fail2ban
github : https://github.com/nextcloud/all-in-one#fail2ban

> Create a file in `/etc/fail2ban/filter.d` named `nextcloud.conf` with the following contents:
```
[Definition]
_groupsre = (?:(?:,?\s*"\w+":(?:"[^"]+"|\w+))*)
failregex = ^\{%(_groupsre)s,?\s*"remoteAddr":"<HOST>"%(_groupsre)s,?\s*"message":"Login failed:
            ^\{%(_groupsre)s,?\s*"remoteAddr":"<HOST>"%(_groupsre)s,?\s*"message":"Two-factor challenge failed:
            ^\{%(_groupsre)s,?\s*"remoteAddr":"<HOST>"%(_groupsre)s,?\s*"message":"Trusted domain error.
datepattern = ,?\s*"time"\s*:\s*"%%Y-%%m-%%d[T ]%%H:%%M:%%S(%%z)?"
```
The jail file defines how to handle the failed authentication attempts found by the Nextcloud filter.

>Create a file in `/etc/fail2ban/jail.d` named `nextcloud.local` with the following contents:
```
[nextcloud]
backend = auto
enabled = true
port = 80,443
protocol = tcp
filter = nextcloud
maxretry = 3
bantime = 86400
findtime = 43200
logpath = /path/to/data/directory/nextcloud.log
chain = DOCKER-USER
```
Ensure to replace `logpath` with your installation's `nextcloud.log` location. If you are using ports other than `80` and `443` for your Web server you should replace those too. The `bantime` and `findtime` are defined in seconds.

>Restart the fail2ban service. You can check the status of your Nextcloud jail by running:
```
fail2ban-client status nextcloud
```
Found an issue as i test this. Check out here for more of  the documentation [[#Fail2ban banning but Brute forcing still possible]]

----
##### Fail2ban limitation
- most the threat actor is  behind a large shared IP pool likes proxies (cloudflare WARP, VPN's, Tor, CG-NAT)
- Fail2ban was designed for the era when one IP = one person
- meaning, fail2ban—in default case—only bans the IP of the service,  not the real IP of a threat actor.

The real is brute-force-throttling that operates at application layer. For nextcloud case it can be verify if its active by running:
```bash
sudo docker exec --user www-data -it nextcloud-aio-nextcloud php occ config:system:get auth.bruteforce.protection.enabled
```
#### Where to find the logs
Located at : 
```
sudo bash
cd /var/lib/docker/volumes/nextcloud_aio_nextcloud/_data/data/
```


##### Fail2ban banning but Brute forcing still possible
This happens during the testing when i implemented the fail2ban with nextcloud AIO

**Problem: *Fail2ban Banning but Brute Force Still Possible***
*I went to my login page and tried 3 attemps of intended wrong credentials  to generate logs in the fail2ban.*
```c
- Fail2ban logs showed `NOTICE [nextcloud] Ban <IP>` successfully 
- Logs immediately continued showing `Found <IP>` entries after the ban 
- Logs escalated from `NOTICE already banned` --to--> `WARNING already banned` 
- `sudo iptables -L DOCKER-USER -n | grep <IP>` returned nothing
```

**Rootcause**: *iptables-nft vs iptables-legacy Split Ruleset*
```
=======On modern Ubuntu/Debian systems, two separate firewall engines coexist: ======

- ====**nftables**====       — where UFW (host firewall) and fail2ban write their rules 
- ===**iptables-legacy**==== — where Docker creates its DOCKER-USER chain
```
*Fail2ban was writing ban rules into nftables. Docker traffic only flows through iptables-legacy DOCKER-USER chain. The two rulesets never met, so bans existed in memory but never in the firewall.*

**DIagnostic Commands**:
```bash
# Confirmed DOCKER-USER chain was empty (ban rules never landed)
sudo iptables -L DOCKER-USER -n -v 

# Confirmed nftables is active with UFW chains inside it 
sudo nft list ruleset 2>/dev/null | head -30
```

**The fix:** *Per-Jail banaction Override (Cleanest Approach) Rather than switching the global iptables alternative (which risks affecting SSH),  surgically targeted only the nextcloud jail to use iptables-legacy.*

>1. Added `banaction = iptables-multiport` to /etc/fail2ban/jail.d/nextcloud.local:
```
[nextcloud]
backend = auto
enabled = true
port = 80,443
protocol = tcp
filter = nextcloud
maxretry = 3
bantime = 86400
findtime = 43200
logpath = /path/to/data/directory/nextcloud.log
chain = DOCKER-USER
#=========THIS ONE BELOW==========
banaction = iptables-multiport
```
