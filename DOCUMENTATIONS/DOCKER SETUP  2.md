
>[!Contents]
>- [[#PREPARATION]]
>- [[#INSTALLATION (NOT FROM APT docker.io)]]
>- [[#ADD DOCKER TO REPOSITORY]]
>- [[#INSTALL DOCKER ENGINE + TOOLS]]
>- [[#CONFIGURATION]]
>- [[#TEST]]

#### **PREPARATION**
   
Update the system
```Bash
sudo apt update && sudo apt upgrade -y
```

Remove old / conflicting Docker installs
```Bash
sudo apt remove docker docker-engine docker.io containerd runc
```
####  **INSTALLATION (NOT FROM APT docker.io)**
```Bash
sudo apt install ca-certificates curl gnupg
```
	
   >[!Note]
	>By default these are already installed 
	>You can verify first by running the command:
	>`apt search <appname>`

Add Docker GPG key
	-This is the purpose of installing the 3 package above.
	
```bash
	
	sudo install -m 0755 -d /etc/apt/keyrings

	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

	sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

Why this matters:
- Prevents tampered packages
- Confirms packages really came from Docker


####  **ADD DOCKER TO REPOSITORY**

```bash
	echo \
	"deb [arch=$(dpkg --print-architecture) \
	signed-by=/etc/apt/keyrings/docker.gpg] \
	https://download.docker.com/linux/ubuntu \
	noble stable" | \
	sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

	`noble` = Ubuntu 24.04 (correct for this machine)


#### **INSTALL DOCKER ENGINE + TOOLS**
   
```Bash
	sudo apt update
	sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```


>[!Summary]-
>	
>**docker-ce** - main engine (deamon)
>**docker-ce-cli** - CLI (docker command)
>**containerd.io** - container runtime (low-level engine)
>**buildx plugin** - advanced build
>**compose plugin** - `docker compose` (modern version)


####  **CONFIGURATION**
```Bash
sudo usermod -aG docker $USER
	
	Then:
newgrp docker
```
	
####  **TEST**
   
   
   `docker run hello-world`
	
 ```console
		
		docker --version
		docker compose version
		docker info
		
		systemctl status docker
 ```
