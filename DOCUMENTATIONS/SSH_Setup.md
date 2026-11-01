

# Setting up ssh server with openssh.
---
*03/29/2026*
*mint cinnamon 22.03 Zena, Kernel 6.8.0, Ubuntu base 24.04 noble*
Purpose: *To keep track of how i configured it and set it up*

>[!Contents]
>- [[#**Check if SSH server is installed**]]
>- [[#**Install SSH Server (Openssh)**]]
>- [[#**Enable service and check status**]]
>- [[#**Configuration File**]]
>- [[#**Test locally**]]
>- [[#**Apply firewall rules**]]
>- [[#**SETTING IT UP WITH KEYPAIRS**]]
>- [[#SECURITY]]
>
>**OTHER INFORMATION**


#### **Uncomplicated Architecture**
```c
Your PC
   │
   ▼
sshd receives connection
   │
   ▼
Authentication
(password/public key)
   │
   ▼
User login succeeds
   │
   ▼
A session is created
   │
   ▼
A shell is started
(bash, zsh, sh...)
   │
   ▼
A shell exist, but still bare minimum state. 
   │
   ▼
A Shell reads ~/.profile
   │
   ▼
.profile runs: source ~/.bashrc
   │
   ▼
.bashrc loads the customizations
   │
   ▼
interactive shell is ready
```

---
####  **Check if SSH server is installed**
---
```Bash
dpkg -l | grep openssh
```
You should see :
`openssh-client` - for client, connecting to diff machine.
`openssh-server` - for servers, to accept ssh connections.

You can also check if its running:
```Bash
	systemctl status ssh
```

If none a message should appear:
*`Unit ssh.service could not be found`* or *`inactive`*

---
#### **Install SSH Server (Openssh)**
---
```Bash
sudo apt update
sudo apt install openssh-server
```
   
#### **Enable service and check status**
```Bash
   sudo systemctl enable ssh *#auto start on boot*
   sudo systemctl start ssh *#START*
   sudo systemctl status ssh *#check if running*
```

Output should be : *`active(running)`*

#### **Configuration File**

^613b03

```Bash
sudo nano /etc/ssh/sshd_config
```
   **Settings** :
```text
Port 22 #default port
PermitRootLogin no #but the options can be prohibit-pass or yes
PasswordAuthentication yes #if want to use password login (disable or comment this if using key pairs instead)
LoginGraceTime 30
```
	   
save and restart : 
```Bash
sudo systemctl restart ssh
```
	
####  **Test locally**
From the same machine(server). Run:
```Bash
ssh your_username@localhost
```

A message will dispay :
*`The authenticity of host 'localhost (127.0.0.1) can't be established.` `fingerprint is [SHA256 here] This is not known by` `any other names.'*
*`Do you want to continue?`* Type yes


#### **Apply firewall rules**

   In terminal:
```Bash
sudo ufw allow ssh
#or
sudo ufw limit from 192.168.1.0/24 to any port 22 proto tcp   
``` 
   
   Its better to recreate the rule than edit it.
   To delete : 
```Bash
sudo ufw status numbered
#then
sudo ufw delete [number]
```
    
   In GUI based.
	   `Name: ssh`
	   `Insert: 0`
	   `Policy: Allow`
	   `Direction: In`
	   `Interface: All interface`
	   `Log: Do not log`
	   `Protocol: Both`
	   `Ip from : [192.168.1.0/24] Ip to [blank]`
	   `Port from : [22] Port to [22]`

---

# **SETTING IT UP WITH KEYPAIRS**

**1.Generate an SSH key pair (do this to the client side)**
```Bash
ssh-keygen -t ed25519 -C "yourComment"
```
   **Breakdown:**
   `-t ed25519` - modern and secure key type
   `-C` - for the comment, for easy identification in scalability. 
   
   **A message will appear :** 
	   `Enter file in which to save the key (home/linuxuser/.ssh/id_ed25519):`
	   Press enter to accept the default location.
	   
   **Then it will ask:**
	`Enter passphrase (empty for no passphrase)`	
	Enter a passphrase for extra security or leave empty for convenience
	
   Result: Two files are actually created in `~/.ssh/` **(on the client machine)**:
	`id_ed25519` -> **private key** (keep it secret, NEVER leave the client machine, do not copy/send it anywhere)
	`id_ed25519.pub` -> **Public key** (safe to share, this is the only one you send to the server)

2.**Copy the public key to the ssh server**

   `theIp` here is the **server/target machine** (not the client), and the path on the server side where the public key ends up is:
   `/home/linuxuser/.ssh/authorized_keys`

```bash
ssh-copy-id linuxuser@theIp
```
   **Breakdown:** this copies the contents of `id_ed25519.pub` (client) into `~/.ssh/authorized_keys` (server, under the `linuxuser` account). If the `.ssh` folder or `authorized_keys` file doesn't exist yet on the server, the command creates them automatically with the correct permissions.

   Manual method (alternative — if `ssh-copy-id` isn't available, e.g. on minimal installs)
```Bash
  cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
  chmod 600 ~/.ssh/authorized_keys
```  

   SCP method (alternative — transfer the pubkey file first, then append it on the server)
```Bash
  # on the client, copy the pubkey to the server's home directory
  scp ~/.ssh/id_ed25519.pub linuxuser@theIp:~/temp_key.pub

  # then ssh into the server (password login must still work at this point)
  ssh linuxuser@theIp

  # on the server, append it to authorized_keys
  mkdir -p ~/.ssh
  cat ~/temp_key.pub >> ~/.ssh/authorized_keys
  chmod 700 ~/.ssh
  chmod 600 ~/.ssh/authorized_keys
  rm ~/temp_key.pub
```  

3.**Test key-based login**
```Bash
ssh linuxuser@ipaddress
```
	
  If setup is correct, it should not ask for pass, only the passphrase if set one.
  if it still ask for password, then check the permission
	`chmod 700 ~/.ssh`
	`chmod 600 ~/.ssh/authorized_keys`

4.**Recommended: Disable password login**
	Refer to  the config at [[#^613b03| OpenSSH config ]]

---

# Cloudflare tunnel
Steps:
- [[#==Step 1 Install Cloudflare in server==]]
- [[#==Step 2 Authenticate the Cloudflared to your Cloudflare account==]]
- [[#==Step 3 Create a tunnel==]]
- [[#==Step 4—Configure the tunnel (config.yml)==]]
- [[#==Step 5 Route the DNS==]]
- [[#==Step 6 Run the cloudflared as systemd service==]]
- [[#==Step 7 (Recommended) Put the cloudflare access in front==]]
- [[#Step 8 Clientside—(Install cloudflared)]]

An option if behind the CGNAT
Overview of the flow:
```
Client (you)          Cloudflare Edge          Server (homelab)
   │                         │                         │
   ├── cloudflared access ──►│◄── cloudflared daemon ──┤
   │   (outbound only)       │    (outbound only)      │
   │                         │                         │
   └── ssh linuxuser@ssh.yourdomain.com ────────────────┘
```
Both sides — client at server — **outbound  only**  connections to  Cloudflare edge. 
CGNAT nat allows outbound connection.

##### ==Step 1: Install Cloudflare in server==

```bash
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null

echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared noble main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

sudo apt update
sudo apt install cloudflared
```
Verify:
```bash
cloudflared --version
```

##### ==Step 2: Authenticate the Cloudflared to your Cloudflare account==
```bash
cloudflared tunnel login
```
This will open a browser and select the domainname you want to have tunnel.
Click authorize.

In the commandline. It will have message :
```
If you wish to copy your credentials to a server, they have been saved to : [path]

#example:
/home/user/.cloudflared/cert.pem
```

##### ==Step 3: Create a tunnel==
```
cloudflared tunnel create <Tunnel_name>
```
It will have a message saying:
```
Tunnel credentials written to ~/.cloudflared/TUNNEL_ID.json, cloudflared chose this file based on where your origin certificate was found. Keep this file secret. To revoke these credentials, delete the tunnel
```
And a tunnel ID:
```
Created tunnel <Tunnel_name> with id: xxxxxxxxxxxxx
```

##### ==Step 4—Configure the tunnel (config.yml)==
```bash 
sudo mkdir -p /etc/cloudflared
sudo nano /etc/cloudflared/config.yml
```

Configuration:
```nano
tunnel: <TUNNEL_ID>
credentials-file: /home/<user>/.cloudflared/TUNNEL_ID.json

ingress:
  - hostname: ssh.yourdomain.com
    service: ssh://localhost:22
  - service: http_status:404
```
**==the path to credential file must be absolute path.==** 

In case you clear the message from step 3
Do this to find the tunnel id:
```bash
cloudflared tunnel list
```

##### ==Step 5: Route the DNS==
```bash
cloudflared tunnel route dns <Tunnel_name> <desired_subdoamin>.yourdomain.com
```
A message will display:
```
Added CNAME <desired_subdoamin>.yourdamin.com which will route to this tunnel tunnelID=xxxxxxxxxxxxxxxxxxxxxxxxx
```

##### ==Step 6: Run the cloudflared as systemd service==
Purpose: no need manual run

```bash
sudo cloudflared service install
sudo systemctl enable --now cloudflared
sudo systemctl status cloudflared
```
Verify:
```bash
journalctl -u cloudflared -f
```

##### ==Step 7: (Recommended) Put the cloudflare access in front==
In cloudflare webpage:
```
Zero trust ——> Access ——> Applications ——> Add an Application ——> self hosted
```
Point to `<yoursubdoamin.yourdoamin.com>`. 
Apply policies

##### Step 8: Clientside—(Install cloudflared)

==For windows:==
```
winget install --id Cloudflare.cloudflared
```
Create a config file at `C:\Users\<YourUser>\.ssh\config`. OR just do : `notepad $env:USERPROFILE\.ssh\config`
```
Host <subdomain>.yourdomain.com
    ProxyCommand cloudflared access ssh --hostname %h
```

Test the connection:
```
ssh <user>@<subdomain>.yourdomain.com
```
This will open a browser to authenticate through the Cloudflare Access policy before establishing the connection.

==For Linux:==
```bash
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null

echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared noble main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

sudo apt update
sudo apt install cloudflared
```
Verify:
```bash
cloudflared --version
```
Note: `cloudflared tunnel login` is NOT needed on the client — that step is only for the server side (creating the tunnel). On the client, `cloudflared` is only used as a proxy helper for the SSH connection.

Create/edit the config file:
```bash
nano ~/.ssh/config
```
```
Host <subdomain>.yourdomain.com
    ProxyCommand cloudflared access ssh --hostname %h
```
Test the connection:
```bash
ssh <user>@<subdomain>.yourdomain.com
```
This will open a browser to authenticate through the Cloudflare Access policy before establishing the connection.
If `cloudflared` is not on PATH, use the absolute path instead (check with `which cloudflared`):
```
ProxyCommand /usr/bin/cloudflared access ssh --hostname %h
```

# SECURITY

1. **Install fail2ban** 
```Bash
sudo apt update && sudo apt install fail2ban
```

2. **Configure fail2ban, make a 'local' copy of the `jail.conf` file in `etc/fail2ban/`**
   ```
   cd /etc/fail2ban
   sudo cp jail.conf jail.local
   ```
   Now edit the file:
```Bash
sudo nano jail.local
```
	
3. **CONFIGURATION**
```text

   [sshd]
   enabled = true
   port = 2222
   filter = sshd
   logpath = /var/log/auth.log
   maxretry = 5
   bantime = 1h  # set to -1 for perma ban
   findtime = 10m
   
   [DEFAULT]
   ignoreip = 127.0.0.1/8 ::1
   destemail = myemail@email.com
   action = %(action_mw)s
```

**Restart the service**
```
   systemctl restart fail2ban
   sudo fail2ban-client status sshd
```

4. Checking status
```Bash
sudo fail2ban-client status sshd
sudo cat /var/log/fail2ban.log.1
```


---


>[!Question]- **POTENTIAL QUESTIONS**
>Not everyone can just copy their keys to the server. So how to login to this ssh server as a fresh user?
>If other method is allowed first to authenticate like `PasswordAuthentication` which can be enable in `etc/ssh/ssh_conf` then that defeats the purpose of avoiding brute forcing.

>[!INFO]-  **STOPING SSH SERVICE**>
>
>Halt the SSH deamon(sshd)
> ```
> sudo systemctl stop ssh
> ```
>
>Disable from starting boot
>```
>sudo systemctl disable ssh
>```
>Stop and disable ssh.socket for security
>```
>sudo systemctl stop ssh.socket
>sudo systemctl disable ssh.socket
>```

>[!Info]- **REMOVING SSH INCLUDING CONFIGS ( FOR FRESH START)**
>Delete port in ufw
>```
> sudo ufw status numbered 
>```
>Then
>```
>sudo ufw delete <number>
>```
> 
>Remove package 
>```
>sudo apt purge openssh-server
>```
>   
>Clean residual files
>```
>sudo apt autoclean
>sudo apt clean
>```
>    
> Check for leftover config files `ls /etc/ssh/`
> If still see files like `sshd_config` , delete it manually 
> ```
> sudo rm -rf /etc/ssh
> ```
> 
> 

> [!WARNING]-
> You can regenerate for fresh keypair with
> ```
> sudo rm /etc/ssh/ssh_host_*
> sudo dpkg-reconfigure openssh-server
> ```
> 
>Removing and regenerating host keys will change the server's identity. Client that previously connected will see a "host key changed" warning and may refure to connect until they update their `known_host`


# COMMON USAGE AND COMMANDS

### ACCESSING LOCAL NETWORK OF SERVER
*Overview: **it’s possible**, but it’s not automatic. You’d need to configure tunneling or use the server as a gateway, and the network must allow it.*
How to: [[Tools#SSH (Secure Shell)]]

depends on how the server and its network are configured: 

- **Basic SSH session**: By default, you only have access to the server itself. You can run commands, transfer files, and interact with that machine, but not automatically with other devices on its local LAN.
    
-  **Network visibility**: If the server has routing or firewall rules that allow it, you can use the server as a “jump host” to reach other machines on the same local network. For example, you might SSH from the server into another device on that LAN.
    
- **SSH tunneling / port forwarding**: SSH supports features like local port forwarding, remote port forwarding, and dynamic SOCKS proxying. These can let you route your traffic through the SSH server, effectively giving you access to services on its local network (if permitted).
	
-  **Restrictions**: Access depends on permissions, firewall rules, and security policies. Some networks deliberately block this kind of lateral access for safety reasons.
