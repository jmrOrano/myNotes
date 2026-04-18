
*Machine: Linux Mint 22.3 Zena, Kernel 6.17-19, Ubuntu 24.04 Noble arch x86_64 64bit*
*Date: Apr/03/2026*
*Purpose : avoid scattered-installtion of dependencies, containing it all inside docker.*
Refer to [[DOCKER SETUP  2 (ChatGPT version)|Docker Installation]] 

1. **PREPARATION**
   Check for Docker Installation
	`docker --version`
	`docker compose version`

   Check for system resource
	`free -h`
	`df -h`

   Create a dir for Minecraft server files
	`mkdir -p ~/minecraft-server`
	`cd ~/minecraft-server`

2. **INSTALL NECESSARY COMPONENTS**
   Choose a Minecraft server docker image
    Popular is the : itzg/minecraft-server

   Create a Docker Compose file(for easier configuration)
    Inside the `~/minecraft-server`, create a `docker-compose.yml`
    `nano ~/minecraft-server/docker-compose.yml`
    
    ```YAML
    
    
    services:
	    minecraft:
		    image: itzg/minecraft-server #it pulls the docker image from the web
		    container_name: minecraft-server #the name of the dir created at prep
		    ports:
			    - "25565:25565"
			    - "24454:24454/udp" # for voice chat
			environment:
				EULA: "TRUE"
				MEMORY: "6G" #can be be change according to performance
				TYPE: "FABRIC" #other options are : VANILLA, FORGE, BUKKIT, PAPER, FOLIA, PURPUR, FABRIC, SPIGOT, QUILT, SPONGEVANILLA........
				CREATE_CONSOLE_IN_PIPE: "true" #for sending commands in game later
			volumes:
				- .data:/data
			restart: unless-stopped
    ```
    

3. **CONFIGURING**
   Adjust environment variables inside the `.yml` file:
    `MEMORY` - Amount of RAM to allocate
	`TYPE` - `SERVER`(VANILLA), `SPIGOT`, `PAPER`(for plugins)

   Persistent data
	`./data` folder will store world, logs,, configs. You can back this folder anytime.

   Optional: Network Settings.
	If planning to serve publicly, make sure port 25565 is open in firewall.


4. **TEST**
   Start the server:
   `docker compose up -d`

	Check logs
	`docker logs -f minecraft-server`

>[!TIPS]
> - You can add plugins/mods if you use Paper or Spigot
> - **Auto backup**: mount a seperate folder or use `cron` to copy `.data/world` periodically
> - **Performance tuning**: Allocate more RAM via `MEMORY`, enable `JAVA_OPTS` for advance options

**SERVING IT PUBLICLY**
1. Find your public IP
	   `curl ifconfig.me`

2. Firewall/Router
	   ```sudo ufw allow 25565/tcp
	   sudo ufw status numbered
	   ```
	   Router:
		   login to router dashboard and do port mapping configuration

3. Networking (OPtional)
	   Docker Network : If plan to multiple services
	   `docker network create`
	   ```for more info refer to official documentation 
	   ```
		Security Tools: 
			ufw for firewall
			Fail2ban (optional) for when you have other services that requires logins.
			
	  DDNS
		  If the ip is `dynamic` then use a Dynamic DNS service like (DuckDNS or No-IP) to assign a hostname to public ip, also to avoid knowing the public ip readable.
	 

4. Configuration
	- Make sure the compose file yml has the correct port.
	  `ports: - "25565-25565"` - the left is host port, the right is container.
	- **Server propterties** -
	  Inside the `./data/server.properties` (the data for the game)
		  -`online-mode=true`
		  -`server-port=25565`
		  -`max-players=20` (adjustable)

	   - **Assigning Server Operators**
	   inside the `data/ops.json`

		```
		[
			{"uuid":"<theUUID>","name":"Playername":"level":4,"bypassPlayerLimit":true},
			
		
		]
		```


>[!Note]
>In level : Options are 1 lowest to 4 the highest
>bypassPlayerLimit - is the condition wherein if player can enter if server is full



**ADDING A VOICE COMS**

1. Install the ff 
	   - Simple Voice chat - at modrinth , Version platform should be Fabric, and version is the current one.
	   - Install the Fabric API [#Fabric Api](https://modrinth.com/mod/fabric-api) , version should same to the simple voice chat

2. Move the them at `/data/plugins`
3. Navigate to `config` folder of the voicehat and change some necessary like port number
4. Restart the server`d   ocker restart <docker ID or name>`
5. Port forwarding the port as similar to the compose file

**FOR CLIENT SIDE**
1. Install the simple voicechat and fabric Installer [#Fabric Installer](https://fabricmc.net/use/installer/)
2. Install the simple voice chat [#Simple Voice Chat](https://modrinth.com/plugin/simple-voice-chat)
3. Run the fabric exe installer
4. Install the fabric Api(not the installer again) at [#Fabrci api]([#Fabric Api](https://modrinth.com/mod/fabric-api)
5. Move the api and the simple voice chat at mods folder of the game

**ADDING SHADERS (Client side)**
*For frabric server*
>[!Requirements]
>- Fabric installer and fabric APi  at [#Fabric](https://fabricmc.net/use/installer/)
>- Sodium at [#Sodium(https://modrinth.com/mod/sodium/versions?version=26.1.1&loader=fabric)]
>- iris shaders at [#Irish Shader](https://modrinth.com/mod/iris?version=26.1.1&loader=fabric)
>- A shader in this guide, its going be to be complementary shader unbound at [#Modrinth](https://modrinth.com/shader/complementary-unbound/versions?version=26.1.1&loader=iris)
>- Move the iris and sodium at mods folder
>- Run the game and navigate to the video settings and do changes



#### BACKING UP THE SERVER 

*Server: Linux mint , Docker compose container*
*Local machine : Windows*
*Tools:"  ssh, scp 

1. **Save and Stop world progress** 
   `docker exec -u 1000 -it <docker-containername> rcon-cli "save-all flush" `
   `docker exec -u 1000 -it minecraft-server rcon-cli "stop"`
2. **Stop the docker**
   `docker stop <docker-containername>`
3. **In client machine:**
   `scp -P 2200 -r linuxuser@112.209.201.227:~/containers/minecraft-server/.data C:\Users\orano\mc-backups\minecraft-data`
4. **Once done copying Start the container again**
   `docker start minecraft-server`
5. Enable the world auto saving again
   `docker exec -u 1000 -it minecraft-sever rcon-cli "save-on"`

