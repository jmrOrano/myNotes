# Docker Reference
> Personal command reference, concepts, and notes — things worth remembering while working with Docker day-to-day.

---

## Table of Contents
- [[#Common Commands]]
  - [[#Containers]]
  - [[#Images]]
  - [[#Storage & Disk Usage]]
  - [[#Docker Compose]]
  - [[#Entering a Container]]
- [[#3 Ways to Run a Container]]
- [[#Notes & Questions]]

---

## Common Commands

### Containers

| Command                          | What it does                                         |
| :------------------------------- | :--------------------------------------------------- |
| `docker ps`                      | List running containers                              |
| `docker ps -a`                   | List all containers (including stopped)              |
| `docker ps -a --size`            | Show disk size per container                         |
| `docker run nginx`               | Pull and run an image                                |
| `docker run -d -p 8080:80 nginx` | Run detached, map host port 8080 → container port 80 |
| `docker stop <id or name>`       | Stop a running container                             |
| `docker start <id or name>`      | Start a stopped container                            |
| `docker rm <id>`                 | Remove a container                                   |
| `docker rm -f <id>`              | Force remove (even if running)                       |
| `docker logs -f <name>`          | Follow live logs                                     |

---

### Images

| Command                  | What it does                                        |
| :----------------------- | :-------------------------------------------------- |
| `docker images`          | List all local images (stored at `/var/lib/docker`) |
| `docker image prune`     | Remove dangling images only (safer)                 |
| `docker system prune -a` | Remove all unused images, not just dangling ones    |

> **Dangling** = images with no tag and no container referencing them. Usually leftover build layers.
>
> Reference: https://docs.docker.com/reference/cli/docker/system/prune/

---

### Storage & Disk Usage

| Command               | What it does                                         |
| :-------------------- | :--------------------------------------------------- |
| `docker system df`    | Summary — what's eating disk space                   |
| `docker system df -v` | Verbose — breakdown per image, container, and volume |

---

### Docker Compose

| Command | What it does |
| :--- | :--- |
| `docker compose up -d` | Start all services defined in `compose.yml` |
| `docker compose -f config.yml up -d` | Use a specific compose filename |
| `docker compose down` | Stop and remove all containers defined in the file |

`docker compose up -d` runs through this sequence automatically:

1. Reads `compose.yml`
2. Pulls images (if not already present)
3. Creates containers
4. Creates the network
5. Starts all services

---

### Entering a Container

```bash
# Enter as default user
docker exec -it <container> bash

# Enter as a specific user (UID 1000)
docker exec -u 1000 -it <container> bash
```

Use `sh` instead of `bash` if the container image is minimal (Alpine-based).

---

## 3 Ways to Run a Container

### `docker compose` — Recommended for most cases

```bash
docker compose up -d
docker compose -f <filename>.yml up -d
```

- Uses a `compose.yml` file to define the entire setup
- Easiest to manage, reuse, and share
- Best for multi-service setups (e.g., app + database + reverse proxy)

---

### `docker run` — Quick one-off runs

```bash
docker run -d -p 8080:80 --name mynginx nginx
```

- Single command, no file needed
- Fine for quick testing
- Gets unwieldy fast once you add volumes, env vars, networks, etc.

---

### `Dockerfile` — Building your own image

```dockerfile
FROM openjdk:17
COPY server.jar /app/
CMD ["java", "-jar", "server.jar"]
```

- Used when you need to build a custom image from scratch
- More control over the base layer
- Useful for understanding how images actually work under the hood

---


## Uninstallation 
---

>**Remove the Docker software only first**
This removes Docker Engine, CLI, Compose plugin, containerd, etc.: reference at : [[DOCKER SETUP  2#**INSTALL DOCKER ENGINE + TOOLS**]]
```Bash
sudo apt purge \
  docker-ce \
  docker-ce-cli \
  containerd.io \
  docker-buildx-plugin \
  docker-compose-plugin
```
Then clean unused dependencies:
```bash
sudo apt autoremove
```

---

>Remove docker's official APT repo and GPG key
Reference file at : [[DOCKER SETUP  2#**ADD DOCKER TO REPOSITORY**]]
```bash
sudo rm /etc/apt/sources.list.d/docker.sources
sudo rm /etc/apt/keyrings/docker.asc
```

Then:
```bash
sudo apt update
```
At this point, Docker's official repository and its specific GPG key are gone.
> Important: this does **not** uninstall GPG itself. The GPG key is just a file used by APT to verify packages from Docker's repository.
---

>**Remove all docker data**
This is the destructive part
```bash
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```
This removes things such as:
- Docker images
- stopped and running container data
- Docker-managed volumes
- Docker networks metadata
- containerd image layers and snapshots

---

>**Optional: remove the `docker` group
>Reference at : [[DOCKER SETUP  2#**CONFIGURATION**]]
```bash
sudo groupde docker
getent group docker # verify if it still exist
```

---

>**Verify the cleanup**
```bash
which docke 
dpkg -l | grep -Ei 'docker|containerd|runc'
ls -l /etc/apt/sources/list.d/
ls -l /etc/apt/keyrings/
systemctl status docker
```
## Notes & Questions

### Where are images stored when using Compose?

Images are **not** stored in the project folder. By default they go into Docker's internal storage:

```bash
sudo ls /var/lib/containerd
```

Root access is required because the permissions are locked down. The image layers are managed by `containerd`, not stored as plain files you can browse.
