
>[!Contents]
>- [[#PREPARATION]]
>- [[#INSTALLATION (NOT FROM APT docker.io)]]
>- [[#ADD DOCKER TO REPOSITORY]]
>- [[#INSTALL DOCKER ENGINE + TOOLS]]
>- [[#CONFIGURATION]]
>- [[#TEST]]
>- [[#**DOCKER COMMON COMMANDS**]]
>- 
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


#### **DOCKER COMMON COMMANDS**
| Commands                                                                                                                          | Des                                                                                                                                                                    |                                                                                                                                       |
| :-------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `docker ps or ps -a`                                                                                                              | list run                                                                                                                                                               |                                                                                                                                       |
| **RUN CONTAINER**                                                                                                                 |                                                                                                                                                                        |                                                                                                                                       |
| `docker run nginx or any service`<br>`docker run -d -p 8080:80 nginx`                                                             | <br>-`d` is for detached (back)<br>- `p` for mapping                                                                                                                   |                                                                                                                                       |
| **LIST CONTAINERS**                                                                                                               | **LIST RUNNING CONTAINERS**                                                                                                                                            |                                                                                                                                       |
| `docker ps`<br><br>`docker ps -a`<br><br>`docker ps -a --size`<br><br><br>`docker system df`<br><br><br><br>`docker system df -v` | - list all (including stopped)<br><br><br><br>- display the size of each container<br><br>-Shows whats eating space<br>-Shows images size, containers, volumes<br><br> |                                                                                                                                       |
| **STOP/STARTagain CONTAINERS**                                                                                                    |                                                                                                                                                                        |                                                                                                                                       |
| ` docker stop  <containerID or Name>`<br><br>`docker start <docker>`                                                              |                                                                                                                                                                        |                                                                                                                                       |
| **REMOVE CONTAINER**                                                                                                              |                                                                                                                                                                        |                                                                                                                                       |
| `docker rm <containerID>`<br>`docker rm -f <containerID>`<br><br>`docker compose down`                                            | <br>-force remove<br><br><br><br>- stops the container and removes                                                                                                     |                                                                                                                                       |
| **REMOVE IMAGES/FREE UP STORAGE**                                                                                                 | **REMOVE IMAGES/FREE UP STORAGE**                                                                                                                                      | **REMOVE IMAGES/FREE UP STORAGE**                                                                                                     |
| `docker system prune -a`                                                                                                          | -Remove all unused, not just dangling ones                                                                                                                             | *Reference: https://docs.docker.com/reference/cli/docker/system/prune/*                                                               |
| `docker image prune`                                                                                                              | -Safer remove just the dangling                                                                                                                                        |                                                                                                                                       |
| **DOCKER COMPOSE**                                                                                                                | **DOCKER COMPOSE**                                                                                                                                                     |                                                                                                                                       |
| `docker compose up -d`<br><br><br><br>                                                                                            | -start a services<br>-`up` - means create + start everything defined in the compose file.<br>-`-d` - detached mode (in background)<br>                                 | 1.Reads the composer.yml<br>  2. Pull images(if not present)<br>  3.Create containers<br>  4.Create network<br>  5.Start all services |
| `docker compose -f config.yml up -d`                                                                                              | specify the filename of config                                                                                                                                         |                                                                                                                                       |
| `docker compose down`<br>`docker logs -f minecraft-server`                                                                        | -stop services<br>-view logs                                                                                                                                           |                                                                                                                                       |
| `docker images`                                                                                                                   | -list images at `/var/lib/docker`                                                                                                                                      |                                                                                                                                       |
| **ENTER DOCKERS**                                                                                                                 |                                                                                                                                                                        |                                                                                                                                       |
| `docker exec -it <container> bash  or sh`<br><br>`docker exec -u 1000 -it <container> bash  or sh`<br>                            |                                                                                                                                                                        |                                                                                                                                       |
|                                                                                                                                   |                                                                                                                                                                        |                                                                                                                                       |

>[!Summary]-
>	
>**docker-ce** - main engine (deamon)
>**docker-ce-cli** - CLI (docker command)
>**containerd.io** - container runtime (low-level engine)
>**buildx plugin** - advanced build
>**compose plugin** - `docker compose` (modern version)


>[!Question] **QUESTIONS AND EXPLORATIONS**
>1. Where is the image file located when using compose?
>   *Not in project folder. By default it is stored inside:*
>   ```Bash
>   sudo ls /var/lib/containerd
>   ```
>- The permission is different so it requires privillage.

>[!Info] Other usefull information
>**THERE ARE 3 WAYS TO SETUP CONTAINERS**
>
>`docker compose` 
>```Bash
>#Example
>docker compose up -d #direct
>docker compose -f <filename> -d #for specified filename
>```
>- is i considered to be the easiest to setup.
>- uses a  YAML file to define the entire setup
>- good for resuable setups
>
>`docker run`
>- Direct oneshot command
>- Just line compose but in a single line command execution
>- Its like remembering all the env in compose file. Good luck remembering the long commands
>
>`dockerfile`
>```Bash
>FROM openjdk:17
COPY server.jar /app/
CMD ["java", "-jar", "server.jar"]
>```
>- For nerds only
>- This is for those who manually build their own image
>- But good for low level understanding



