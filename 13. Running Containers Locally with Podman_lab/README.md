# Lab 13: Running Containers Locally with Podman

## 🎯 Objectives
By the end of this lab, you will be able to:
- Run containers in both **foreground** and **detached** modes  
- Configure **port mapping** between host and container  
- Manage **persistent storage** with volumes  
- Override user settings at runtime  
- Inspect running containers and their configurations  

---

## 🛠 Prerequisites
- Linux system with Podman (version 3.0+ recommended)  
- Basic Linux command-line knowledge  
- Internet access to pull container images  

---

## ⚙️ Setup Requirements
Install Podman (if not already installed):

```bash
# RHEL/CentOS/Fedora
sudo dnf install -y podman

# Debian/Ubuntu
sudo apt-get install -y podman
```
Verify installation:

```
podman --version
```
---

## 🚀 Tasks
# Task 1: Running Containers in Foreground and Background
## Subtask 1.1: Run a Container in Foreground
```
podman run --name foreground-container docker.io/library/nginx
```
Starts NGINX container & attaches logs to your terminal

Stop with Ctrl+C

## Subtask 1.2: Run a Container in Detached Mode
```
podman run -d --name background-container docker.io/library/nginx
```
- -d runs in background
- Verify:

```
podman ps
```
---

# Task 2: Port Mapping and Volume Binding
## Subtask 2.1: Bind Container Ports to Host
Map container port 80 → host port 8080:

```
podman run -d --name webapp -p 8080:80 docker.io/library/nginx
```
Verify access:

```
curl http://localhost:8080
```
✅ Expected: NGINX welcome page

## Subtask 2.2: Persistent Storage with Volumes
Create volume:

```
podman volume create mydata
```
Run container with volume:

```
podman run -d --name vol-container -v mydata:/data docker.io/library/alpine tail -f /dev/null
```
Verify:

```
podman exec vol-container ls /data
```
---

# Task 3: User Management and Runtime Overrides
## Subtask 3.1: Override User at Runtime
Run as UID 1000:

```
podman run --rm -it --user 1000 docker.io/library/alpine whoami
# Expected: "whoami: unknown uid 1000"
```
Run as nobody:
```
podman run --rm -it --user nobody docker.io/library/alpine whoami
# Expected: nobody
```
## Subtask 3.2: Inspect Running Containers
Get detailed info:

```
podman inspect webapp
```
View logs:

```
podman logs webapp
```
Check resource usage:

```
podman stats
```
---

# Task 4: Cleanup
Stop all containers:

```
podman stop -a
```
Remove containers:

```
podman rm -a
```
Remove volumes (optional):
```
podman volume rm mydata
```
---

## 🛠 Troubleshooting Tips
- Permission issues → try --privileged

- Port conflicts → check with:

```
ss -tulnp
```
Container exits immediately → check logs:

```
podman logs <container>
```
---

# ✅ Conclusion
You have learned:

- Running containers in foreground & detached modes

- Configuring port mappings for network access

- Using volumes for persistence

- Managing users at runtime

- Inspecting containers with logs & stats

These are foundational Podman skills for dev & production work.

---

## 📚 Next Steps
- Explore Podman pods for multi-container apps

- Try rootless mode for security

- Experiment with health checks & auto-restart policies
