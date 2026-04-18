

### Setting up ssh server with openssh.
*03/29/2026*
*mint cinnamon 22.03 Zena, Kernel 6.8.0, Ubuntu base 24.04 noble*
Purpose: *To keep track of how i configured it and set it up*

1. **Check if SSH server is installed**
   `dpkg -l | grep openssh`
		You should see :
		`openssh-client` - for client, connecting to diff machine.
		`openssh-server` - for servers, to accept ssh connections.
		
	You can also check if its running:
		*`systemctl status ssh`*
		If none a message should appear:
		*`Unit ssh.service could not be found`* or *`inactive`*


2. **Install SSH Server (Openssh)**
   *`sudo apt update`*
   *`sudo apt install openssh-server`*


3. **Enable service and check status**
   `sudo systemctl enable ssh` *#auto start on boot*
   `sudo systemctl start ssh` *#START*
   `sudo systemctl status ssh` *#check if running*

	Output should be : *`active(running)`*

4. **Configuration File**
   *`sudo nano /etc/ssh/sshd_config`*
   Settings :
	   *`Port 22`* - default port
	   *`PermitRootLogin no`* - but the options can be *`prohibit-pass`* or *`yes`*
	   *`PasswordAuthentication yes`* - if want to use password login (disable or comment this if using `key pairs instead)`
	   `LoginGraceTime 30`
	save and restart : *`sudo systemctl restart ssh`*

5.  Test locally
	From the same machine(server). Run: `sudo` `systemctl your_usename@localhost`
		A message will dispay : *`The authenticity of host 'localhost (127.0.0.1) can't be established.` `fingerprint is [SHA256 here] This is not known by` `any other names.'*
	*`Do you want to continue?`* Type yes


6. Apply firewall rules
   In terminal *`sudo ufw allow ssh`*
   *or `sudo ufw limit from 192.168.1.0/24 to any port 22 proto tcp`*
   
   Its better to recreate the rule than edit it.
   To delete : *`sudo ufw status numbered`* then *`sudo ufw delete [number]`*
   
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

### **SETTING IT UP WITH KEYPAIRS**

1.**Generate an SSH key pair (do this to the client side)**
   `*ssh-keygen -t ed25519 -C "yourComment"*`

   `-t ed25519` - modern and secure key type
   `-C` - for the comment, for easy identification in scalability. 
   
   A message will appear : 
	   `Enter file in which to save the key (home/linuxuser/.ssh/id_ed25519):`
	   Press enter to accept the default location.
	   
   Then it will ask:
	`Enter passphrase (empty for passphrase)`	
	Enter a passphrase for extra security or leave empty for convenience
	
   Result: Two files are actually created in `~/.ssh/`:
	`id_ed25519` -> **private key** (keep it secret)
	`id_ed25519.pub` -> **Public key** (to share with server)

2.**Copy the public key to the ssh server**
	`ssh-copy-id linuxuser@theIp`  
	 
  Manual method (alternative)  
	  `cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys`
	  `chmod 600 ~/.ssh/authorized_keys`

3.**Test key-based login**
	`ssh linuxuser@ipaddress`  
	
  If setup is correct, it should not ask for pass, only the passphrase if set one.
  if it still ask for password, then check the permission
	`chmod 700 ~/.ssh`
	`chmod 600 ~/.ssh/authorized_keys`

4.**{OPTIONAL}Disable password login**
	Refer to number of 4 of [[#Setting up ssh server with openssh.]]

---


>[!Question]- **POTENTIAL QUESTIONS**
>1. Not everyone can just copy their keys to the server. So how to login to this ssh server as a fresh user?
>>If other method is allowed first to authenticate like `PasswordAuthentication` which can be enable in `etc/ssh/ssh_conf` then that defeats the purpose of avoiding brute forcing.

>[!INFO]-  **STOPING SSH SERVICE**
>
>
>1. Halt the SSH deamon(sshd)
>   `sudo systemctl stop ssh`
>   
>2. Disable from starting boot
>   `sudo system disable ssh`
>  
> 3. Stop and disable ssh.socket for security
>    `sudo systemctl stop ssh.socket`
>    `sudo systemctl disable ssh.socket`

>[!Info]- **REMOVING SSH INCLUDING CONFIGS ( FOR FRESH START)**
>
>
>1. Delete port in ufw `sudo ufw status numbered` then `sudo ufw delete <number>`
>2. Remove package 
>   `sudo apt purge openssh-server`
>   
> 3. Clean residual files
>    `sudo apt autoclean`
>    `sudo apt clean`
>    
>  4. Check for leftover config files `ls /etc/ssh/`
>  5. If still see files like `sshd_config` , delete it manually `sudo rm -rf /etc/ssh`
> 

> [!WARNING]-
> You can regenerate for fresh keypair with
> ```
> sudo rm /etc/ssh/ssh_host_*
> sudo dpkg-reconfigure openssh-server
> ```
> 
>Removing and regenerating host keys will change the server's identity. Client that previously connected will see a "host key changed" warning and may refure to connect until they update their `known_host`


### SECURITY

1. Install fail2ban 
   `sudo apt update && sudo apt install fail2ban`
2. Configure fail2ban, make a 'local' copy of the `jail.conf` file in `etc/fail2ban/`
   ```
   cd /etc/fail2ban
   sudo cp jail.confi jail.local
   ```
   Now edit the file:
   `sudo nano jail.local`
3. CONFIGURATION
   ```
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
   
   [DEFAULT]
   destamil = myemail@email.com
   action = %(action_mw)s
   
   systemctl restart fail2ban
   sudo fail2ban-client status sshd
   
   ```