July 18, 2026

Setup:
```
Thinkpad L490 | Kernel: 6.17.9-23-generic | arch: x86_64 |
os-release: Linux Mint| Version 22.3(Zena) | Ubuntu 24.04 Noble
```

# Draft Note Now

## Security Information and Event Management
*A system that colects security-relevant events from multiple sources, that makes them searchable, analyze them and generates alerts when pattern appeas suspicious.*

Raw log:
```
Failed password for invalid user admin from 192.0.2.50 port 54321
```
For SIEM interpretation :
```
{
    timestamp: "2026-07-23 18:30:00",
    event_type: "authentication_failure",
    service: "sshd",
    username: "admin",
    source_ip: "192.0.2.50",
    result: "failure"
}
```
The process in abstract level:
```
RAW LOG
   ↓
PARSING
   ↓
STRUCTURED EVENT
```
## Draft Listing of persistent log files and services in my system
---
```
SERVICES DOCKERIZED =======================
1. minecraft: /home/linuxuser/containers/minecraft-server/.data/logs/
2. nextcloud login logs : /var/lib/docker/volumes/nextcloud_aio_nextcloud/_data/data/
   -requires sudo to read and access
   -not sure if can a SIEM tool read this directly
3. nginx-proxy-manager logs : /home/linuxuser/containers/nginx-proxy-manager/data/logs/
   - proxy-host-1_access.log
   - proxy-host-1_error.log
   - fallback_http_error.log
   - fallback_http_access.log
   
   
HOST & SYSTEM LOGS =============================
4. SSH log : /var/log/auth.log
   -for sudo, for ssh login
5. Fail2ban log : /var/log/fail2ban.log
   -for login attempt with nextcloud, and ssh
KERNEL LOGS=====================================
6. kernel log: /var/log/kern.log
	- hardware, driver, kernel events
   
BOOT L0G========================================
7. boot log : /var/log/boot.log
	- stores seystem starup messages or error messages, driver loading status and init scripts
	- less priority for SIEM (i guess?)
	  
NETWORKING LOG==================================
8. ufw log : /var/log/ufw.log
   
DOCKER LOGS====================================
9. docker logs -f minecraft-server
   - same as the log located at /home/
7. docker logs -f nextcloud-aio-nextcloud
	- different output compared to reading the application generated log at /var/lib
8. docker logs -f nextcloud-aio-mastercontainer
   - generate log seldom
9. docker logs -f nextcloud-aio-apache
   - rarely to never generate logs, maybe becuase i already have NPM as a reverse proxy.
10. docker logs -f nextcloud-aio-redis
```

---
## Log grouping based on priority

| Priority | Log Source                                    | Location / Access                                                                                                                                 | Why                                                                                          | CATEGORY                | Answers the question:                 |
| :------- | --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- | ----------------------- | ------------------------------------- |
| HIGH     | SSH Authentication                            | `/var/log/auth.log`                                                                                                                               | Detects, brute force<br>- unauthorize login attempts<br>-successful logins<br>-sudo activity | Authentication & access | Who tried to get in, and did it work? |
| HIGH     | Fail2ban logs                                 | `/var/log/fail2ban.log`                                                                                                                           | Shows : <br>-detected attacks<br>-banned IP's<br>- automated security response               | Detection/ Response     | What did my defenses do about it?     |
| HIGH     | Nextcloud app logs                            | `/var/lib/docker/volumes/nextcloud_aio_nextcloud/_data/data/nextcloud.log`                                                                        | user authentication, <br>-failed logins,<br>- account activity, <br>-app events              | Application Layer       | Whats happening inside?               |
| HIGH     | nginx proxy manager access log and error logs | `/home/linuxser/containers/nginx-proxy-manager/data/logs/`                                                                                        | External HTTP requests, client IPs, suspicious web traffic pattern                           | Permiter Network        | Whats hitting my network edge?        |
| HIGH     | ufw  firewall                                 | `/var/log/ufw.log`                                                                                                                                | Network blocks, rejected connections, scanning attemps.                                      | Perimeter Network       |                                       |
| MEDIUM   | Docker Conainer Logs                          | `docker logs <container_name>`<br>`Minecraft server logs`<br>`docker logs nextcloud-aio-mastercontianer`<br>`docker logs nextcloud-aio-nextcloud` | Useful for failures, crashes, abnormal container behavior                                    | Container Platform      |                                       |
| LOW      | Kernel logs                                   | `/var/log/kern.log`                                                                                                                               | Mostly hardwae, driver, and kernel-level events: useful only mainly for investigation        |                         |                                       |
| LOW      | Boot log                                      | `/var/log/boot.log`                                                                                                                               | Startup sequence information; rarely needed for continuous security monitoring               |                         |                                       |

---
## SIEM Path
---
```
Security Control
        ↓
  Fail2ban detects attacks
          ↓
    Logs are generated
            ↓
      SIEM collects those logs
              ↓
        SIEM correlates events
                ↓
          SIEM improves visibility
```

---

## Choosing a SIEM Tool
*July 23, 2026*

### Wazuh
*July 24, 2026*

Full SIEM Platform. 
The most direct option for understanding actual SIEM workflow. 
```
Host Machine --> [SSH,Nextcloud,Docker,other services] --> Wazuh Agent --> Wazuh Server --> Wazuh Indexer --> Wazuh Dashboard
```
Has the ff:
- Log collection
- Parsing and Decoding
- Detection Rules
- Alerting
- File integrity monitoring
- Vulnerability detection
- Configuration assessment
- Active Reporting

Poenitentia Learning from using Wazuh:
```
Custom Log Collection
Decoders
Rules
Rule Correlation
Active Response
File Integrity Monitoring
Vuln Dectection
Incident Investigation
```

---

### Graylog
*July 24, 2026*

Much simpler architecture compared to Wazuh.
Mainly a tool for General and Centralized Log Management. 


Has the ff feature and Potential Learning
```
Syslog
Inputs
Parsing
Pipelines
Streams
Search
Dashboard
Evet Definitions
```
Uses the component of:
```
Opensearch/Elasticsearch - storage layer
MongoDB - For Metadata storage
```

---

### Grafana Loki + Promtail
Low resource footprint. 
Runs 3 components: Promtail, Loki, Grafana) 
```
Promtail - The agent that runs on host and reads log files then send to Loki
Loki - The storage/indexing engine, index only the keyword/label instead of the whole log
Grafana - The Dashboard UI
```
Not a SIEM in a strict sense, more common for a log aggregator platyform.
No concept of threat detection.
No Threat detection rules
No active response

| Tool                  | Performance Cost                                                                                                                                                                   | Core Purpose and Focus                                                                                                                                     | How do they work?                                                                                                                                                                      |
| :-------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Wazuh                 | - Manger written in C, lower footprint: ~200-500mb<br>- Indexer uses OpenSearch, JVM Based ~ 4GB head recommended<br>- Dashboard(Node.js based) - ~200-500mb                       | Built for threat detection, Integrity monitoring, regulatory compliace and incidient response                                                              | Uses an agent-based setup sending data to a manager, indexing extensive security metadata usually into OpenSearch or Elasticsearch backend storage                                     |
| Graylog               | - JVM Based server ~1-2GB heap<br>- Elastic/Opensearch, same engine as Wazuh ~2-4gb heap<br>- uses MongoDB ~200-500Mb heap<br>- Built in dashboard at Server, no seperate process. | Operates as a centralized log management tool optimized for general IT operations,<br>- Fast full-text searching, parsing, and application troubleshooting | : Relies on a server processing engine backed by MongoDB (for metadata) and Elasticsearch/OpenSearch (for full-text indexing of every single word                                      |
| Grafan Loki+ Promtail | Massive cost savings and low storage footprint by avoidiung full-text indexing, though searching raw log content is less flexible                                                  | Lightweight, developer-focused log aggregator system<br>-                                                                                                  | Uses Promtail as an agent to ship logs, while Loki indexes only metadata labels (like app name or environment) rather than full text, storing compressed log chunks in object storage. |


---

# Wazuh dockerized
Official Github Repo : https://github.com/wazuh/wazuh
For dockerized option : https://github.com/wazuh/wazuh-docker
For official documentattion of the dockerized option : [https://github.com/wazuh/wazuh-docker/tree/main/docs](https://documentation.wazuh.com/current/deployment-options/docker/index.html)

## Abstract Architecture 
The variables are :
- Log Sources - coming from the application and services.
- Wazuh Agent - read log files, forward events
- Wazuh Manager - The decoders, Rules, Correlation, and Analysis
- Wazuh Indexer(OpenSearch) - Store events, Search data 
- Dashboard - For visualization, for search, and investigate
```
┌──────────────────────────────────────────────────────┐
│                    LOG SOURCES                       │
│                                                      │
│  SSH     Nextcloud     Docker     Nginx     UFW      │
│   │          │           │          │        │       │
└───┼──────────┼───────────┼──────────┼────────┼───────┘
    │          │           │          │        │
    └──────────┴───────────┴──────────┴────────┘
                       │
                       ▼
              ┌────────────────┐
              │  WAZUH AGENT   │
              │                │
              │ Log Collector  │
              │ FIM            │
              │ System Monitor │
              └───────┬────────┘
                      │
                      ▼
              ┌────────────────┐
              │ WAZUH MANAGER  │
              │                │
              │ Decoders       │
              │ Rules          │
              │ Correlation    │
              │ Analysis       │
              └───────┬────────┘
                      │
                      ▼
              ┌────────────────┐
              │ WAZUH INDEXER  │
              │                │
              │ Store Events   │
              │ Search Data    │
              └───────┬────────┘
                      │
                      ▼
              ┌────────────────┐
              │   DASHBOARD    │
              │                │
              │ Search         │
              │ Visualize      │
              │ Investigate    │
              └────────────────┘
```

---
## Deployment Options 
---
Reference at : https://documentation.wazuh.com/current/deployment-options/docker/wazuh-container.html#deployment-options

>**Single-node stack** – spins up exactly one container per core component (mangerr, Indexer, and Dashboard)
```
1 Docker Host
│
├── 1 Wazuh Manager container
├── 1 Wazuh Indexer container
└── 1 Wazuh Dashboard container
```

>**Multi-node stack** – can be more than 1 of manager, Indexer, for the redundancy and distributed workload
```
Docker Host
│
├── wazuh-manager-master container
├── wazuh-manager-worker container
│
├── wazuh-indexer-1 container
├── wazuh-indexer-2 container
├── wazuh-indexer-3 container
│
├── wazuh-dashboard container
└── nginx container
```

>**Wazuh Agent** – Deploying the agent in containerized way means it cannot directly access or monitor the host system.
	- With that, this documentation will proceed to install the Agent directly in host considering most of my log sources are in the host machine.
```
Wazuh Agent
      │
      └── Host system
          ├── /var/log
          ├── processes
          ├── filesystem
          └── system information
```


---

## Prerequisites
---
Reference at: https://documentation.wazuh.com/current/deployment-options/docker/wazuh-container.html#prerequisites

>This documentation opted to use single node stack. So listed below is only for single node stack:
```
OS: Linux or Windows
Architecture: AMD64 (x86_64) or ARM64 (AARCH64)
CPU: At least 4 cores
Mem: At least 8GB or RAM for the Docker Host
Disk Space: At least 50Gb for Docker images and Data Volumes
```

For docker intallation: [[DOCKER SETUP  2]]

---
**Docker host requirements** 
Set the `max_map_count` to `262144` on docker host. 
- It because the Wazuh Indexer (OpenSearch) uses a lot of virtual memory areas (VMAs), so that it can access the files directly from disk as if they were in RAM.

```bash
sysctl -w vm.max_map_count=262144
```

>[!Warning] 
>Not setting it to `262144`, the indexer might fail due to limited virtual memory mapping.

---

## Deploying Central Components
---
Single-node deployment

#### Cloning the repo
---
Official repo: https://github.com/wazuh/wazuh-docker
```
git clone https://github.com/wazuh/wazuh-docker.git -b v4.14.6
```

Then navigate inside the `single-node` directory
```
cd ~/user/<path>/wazuh-docker/single-node
```

---
#### Certificate Generation
---
The `OpenSearch`(Wazuh indexer) requires a TLS/SSL encryption to communicate to other core components inside-outside even thought the traffic is internal inside the Docker network.
```
Manager  <--TLS-->  Indexer  <--TLS-->  Dashboard
```

Running the ff command to generate the desired certificates:
```bash
docker compose -f generate-indexer-cert.yml run --rm generator
```
The generated certificates will be stored in the `wazuh-docker/single-node/config/wazuh_indexer_ssl_certs` directory.

---
#### Deployment 
---
The plan is for the service to be only accessible via loopback instead of all listening to all network (`0.0.0.0`)
	- This is minimize attack surface even inside the LAN
Review the compose file first. In my case my machine has a port that is already being used (e.i port 443)

Under the `wazuh.dashboard`.  
```
  wazuh.dashboard:
    image: wazuh/wazuh-dashboard:4.14.6
    hostname: wazuh.dashboard
    restart: always
    ports:
      - 443:5601
```
I change the port to :
```
    ports:
      - 127.0.0.1:8443:5601
```

Under the `wazuh.manager`
```
    ports:
      - "1514:1514"
      - "1515:1515"
      - "514:514/udp"
      - "55000:55000"
```
Changing the port to:
```
    ports:
      - "127.0.0.1:1514:1514"
      - "127.0.0.1:1515:1515"
#"514:514/udp"
      - "127.0.0.1:55000:55000"

```
`1514` is for the `Agent --> manger` communication. `1515` is for agent new registration. `514` is commented because its only for external devices with remote syslog, `55000` is the server API used by dashboard to query 


After the review and changes, its ready for deployment:
```bash
cd ~/<path_to_cloned_repo>/single-node
docker compose up -d
```
- In accessing the dashboard it can now only be done either via local port forwarding tunnel SSH or accessing the physical machine and typing `https://localhost:8443`
- The default password of initial login is  username: `admin`, password: `SecretPassword`

---

## Installing Agent
---
Official reference: https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-linux.html

The wazu agent will not be deployed in dockerized way. It is because it needs to directly access or monitor the host system. 

#### Adding the Wazu Repository
---
Requires root. Do `sudo bash` first for smooth process.

>Install the ff packages if missing
```
apt-get install gnupg apt-transport-https
```

>**Install the GP**
```
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
```

>**Add the repository**
```
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

>**Update the package information**
```
apt-get update
```

---

#### Deploying the Wazuh agent
---
Requires sudo:
```bash
WAZUH_MANAGER="127.0.0.1" apt-get install wazuh-agent
```

>**Enable and start the Wazuh agent service**
```
systemctl daemon-reload
systemctl enable wazuh-agent
systemctl start wazuh-agent
```

---

#### Disable Wazuh Updates
```Quote
Compatibility between the Wazuh agent and the Wazuh manager is 
guaranteed when the Wazuh manager version is later than or equal 
to that of the Wazuh agent.
```

Run:
```
sed -i "s/^deb /#deb/" /etc/apt/sources.list.d/wazuh.list
apt-get update
```

Alternatively, you can set the package state to `hold`. This action stops updates but you can still upgrade it manually using `apt-get install`.
```
echo "wazuh-agent hold" | dpkg --set-selections
```

---

#### Verify versions

For wazuh agent:
```
sudo /var/ossec/bin/wazuh-control info -v
```

For manager: its at the compose file 
```
services:
  wazuh.manager:
    image: wazuh/wazuh-manager:4.14.6
```

---

## Verification 
---
After all the installation process. Its time to verify if the agent is connected. 

#### Check if the agent is connected
---
There are 2 ways:

>Dashboard
```
https://localhost:8443 ---> Agent Management ---> Summary
```
The  hostname should have a status of `Active` and green.

>Via cli
```
docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/agent_control -l
```

----
#### Check for the default whats being monitored by agent
----
A fresh installed agent have a default config. 
Open it  at:
```
sudo cat /var/ossec/etc/ossec.conf | grep -A 2 "<localfile>"
```


---

## Adding log sources 
---
July 29, 2026
### Adding the fail2ban log 
---
Navigate and edit the config at :
```
sudo nano /var/ossec/etc/ossec.conf
```

Inside the tag of `<ossec_config> </ossec_config>` block. Or find where the `journald` localfile is located. Add the ff:
```
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/fail2ban.log</location>
</localfile>
```

>Restart the agent:
```bash
sudo systemctlrestart wazuh-agent
```

>Test by doing the ff
```
– Generate a login failed 
– Read the fail2ban log at: /var/log/fail2ban.log
– Copy at least 1 line of raw log
– Go inside the docke to do logtest
	-> docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/wazuh-logtest
– Paste the raw log	  
– It should have a message like :

**Phase 1: Completed pre-decoding.
        full event: '2026-07-28 19:27:27,094 fail2ban.filter         [345215]: INFO    [sshd] Found <myiphere> - 2026-07-28 19:27:26'
        timestamp: '2026-07-28 19:27:27,094'

**Phase 2: Completed decoding.
        No decoder matched.
```
The `No decoder matched` should be expected.

### Adding a custom decoder
---
#### For Ban decoder:
---
Reference
```
2026-07-30 15:56:03,337 fail2ban.actions        [345215]: NOTICE  [nextcloud] Ban 162.xxx.xxx.132
```

Go inside the docker
```bash
docker exec -it single-node-wazuh.manager-1 bash
```

Then add the content to the xml file inside the docker:
```bash
cat > /var/ossec/etc/decoders/local_decoder.xml << 'EOF'
<decoder name="fail2ban">
    <prematch>[\d+]: \w+\s+[\w+]</prematch>
</decoder>

<decoder name="fail2ban-ban">
    <parent>fail2ban</parent>
    <prematch>] Ban \S+</prematch>
    <regex>[(\w+)] Ban (\S+)</regex>
    <order>jail, srcip</order>
</decoder>
EOF
exit
```

Then restart:
```bash
docker restart single-node-wazuh.manager-1
```

Do a test:
```
docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/wazuh-logtest

#AND PASTE A LOG======================
2026-07-20 09:16:08,067 fail2ban.actions         [2113606]: NOTICE  [sshd] Ban <ip_here>
```

---
#### For Unban decoder:
---
Reference log:
```
2026-07-31 15:56:02,676 fail2ban.actions        [345215]: NOTICE  [nextcloud] Unban 162.xxx.xxx.132
```

Go inside the docker:
```
docker exec -it single-node-wazuh.manager-1 bash
```

Then add the ff to the `/var/ossec/etc/decoders/local_decoder.xml`
```bash
<decoder name="fail2ban-unban">
  <parent>fail2ban</parent>
  <prematch>] Unban \S+</prematch>
  <regex>[(\w+)] Unban (\S+)</regex>
  <order>jail, srcip</order>
</decoder>
```

Then restart:
```
docker restart single-node-wazuh.manager-1
```

---

### Adding a Rule
---
Even though the log is decoded, it wont appear in index unless we make one.

Also : Read reminder first: [[#Reminder]]

#### For Ban rule:
---
Go inside the docker:
```bash
docker exec -it single-node-wazuh.manager-1 bash
```

Add to the local_rules.xml:
```bash
cat > /var/ossec/etc/rules/local_rules.xml << 'EOF'
cat > /var/ossec/etc/rules/local_rules.xml << 'EOF'
 <group name="fail2ban,">
  <rule id="100100" level="8">
   <decoded_as>fail2ban</decoded_as>
   <match>] Ban </match>
   <description>Fail2ban has banned an IP: $(srcip) from jail: $(jail)</description>
   <group>authentication_failures,</group>
  </rule> 
</group>
EOF
exit
```

Restart:
```bash
docker restart single-node-wazuh.manager-1
```

---
#### For Unban Rule:
---
Go inside the docker:
```bash
docker exec -it single-node-wazuh.manager-1 bash
```

Add the ff to the `/var/ossec/etc/rules/local_rules.xml`:
```bash
<rule id="100101" level="3">
    <decoded_as>fail2ban</decoded_as>
    <match>] Unban </match>
    <description>Fail2ban has unbanned an IP: $(srcip) from jail: $(jail)</description>
    <group>authentication_failures,</group>
  </rule>
```

Restart:
```bash
docker restart single-node-wazuh.manager-1
```

---

## Testing
---
Run
```Bash
docker exec -it single-node-wazuh.manager-1 /var/ossec/bin/wazuh-logtest
```

AND PASTE A LOG
```bash
#======There should be phase 3
2026-07-31 15:56:02,676 fail2ban.actions        [345215]: NOTICE  [nextcloud] Unban 162.XXX.XXX.132

#=====There should be phase 3:
2026-07-20 09:16:08,067 fail2ban.actions         [2113606]: NOTICE  [sshd] Ban 162.158.118.132

#======There should only be 
2026-07-30 15:55:56,790 fail2ban.filter         [345215]: INFO    [nextcloud] Found 162.XXX.XXX.132 - 2026-07-30 15:55:56
```

## Reminder
---
Avoid using a [[Whatis#Heredoc|heredoc]] approach when [[#Adding a Rule]] , *though its the approach used in this documentation, still dont use it.* 
- Install nano instead `yum install nano` inside the docker