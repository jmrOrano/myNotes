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

## Notes & Questions

### Where are images stored when using Compose?

Images are **not** stored in the project folder. By default they go into Docker's internal storage:

```bash
sudo ls /var/lib/containerd
```

Root access is required because the permissions are locked down. The image layers are managed by `containerd`, not stored as plain files you can browse.
