# Lab 15: Inspecting Containers and Images with Podman

## 🎯 Objectives
By the end of this lab, you will be able to:
- Use `podman inspect` to examine container and image metadata  
- Extract and analyze environment variables  
- Review volume mounts and network port configurations  
- Interpret container status and exit codes for troubleshooting  

---

## 🛠 Prerequisites
- Linux system with Podman installed (RHEL/CentOS/Fedora recommended)  
- Basic familiarity with terminal commands  
- A running container to inspect (we’ll create one in this lab)  

---

## ⚙️ Lab Setup
Install Podman if not already installed:
```bash
sudo dnf install -y podman    # RHEL/CentOS/Fedora
```
```bash
sudo apt-get install -y podman  # Debian/Ubuntu
```

Verify installation:

```
podman --version
```
Pull a sample image:

```
podman pull docker.io/library/nginx:alpine
```
---

## 🚀 Tasks
# Task 1: Inspecting Container and Image Metadata
## Subtask 1.1: Basic Inspection
Run a container:

```
podman run -d --name my_nginx -p 8080:80 nginx:alpine
```
Inspect the container:

```
podman inspect my_nginx
```
✅ JSON output showing runtime details

Inspect the image:

```
podman inspect nginx:alpine
```
👉 Image inspection = static config, Container inspection = runtime state

## Subtask 1.2: Filtering Specific Information
Get container IP:

```
podman inspect my_nginx --format '{{.NetworkSettings.IPAddress}}'
```
Get container creation time:

```
podman inspect my_nginx --format '{{.Created}}'
```
---

# Task 2: Extracting Environment Variables
## Subtask 2.1: Viewing All Environment Variables
Run with custom env vars:

```
podman run -d --name env_test -e APP_COLOR=blue -e APP_MODE=prod nginx:alpine
```
Inspect env vars:

```
podman inspect env_test --format '{{.Config.Env}}'
```
## Subtask 2.2: Finding Specific Variables
Check for APP_COLOR:

```
podman inspect env_test --format '{{range .Config.Env}}{{if eq (index (split . "=") 0) "APP_COLOR"}}{{.}}{{end}}{{end}}'
```
---

# Task 3: Reviewing Volume Mounts and Ports
## Subtask 3.1: Examining Volume Mounts
Run with volume:

```
podman run -d --name vol_test -v /tmp:/container_tmp nginx:alpine
```
Inspect mounts:

```
podman inspect vol_test --format '{{.Mounts}}'
```
## Subtask 3.2: Analyzing Port Mappings
Inspect ports:

```
podman inspect my_nginx --format '{{.NetworkSettings.Ports}}'
```
Get only host port:

```
podman inspect my_nginx --format '{{(index (index .NetworkSettings.Ports "80/tcp") 0).HostPort}}'
```
---

# Task 4: Analyzing Container Status and Exit Codes
## Subtask 4.1: Checking Running Status
Run a failing container:

```
podman run --name fail_test alpine sh -c "exit 3"
```
Check exit code:

```
podman inspect fail_test --format '{{.State.ExitCode}}'
```
👉 Non-zero exit code = error

## Subtask 4.2: Comprehensive State Analysis
Inspect full state:

```
podman inspect my_nginx --format '{{json .State}}' | jq
```
(Install jq if needed: sudo dnf install jq)


## 🧹 Cleanup
```
podman stop my_nginx env_test vol_test
podman rm my_nginx env_test vol_test fail_test
podman rmi nginx:alpine
```
---

# ✅ Conclusion
In this lab, you learned how to:

- Extract detailed metadata from containers and images

- Analyze environment configurations

- Review storage volumes and port mappings

- Troubleshoot with exit codes and container state

These skills are essential for debugging containerized apps and understanding their runtime behavior.

---

## 🧩 Additional Exercises
- Create a container with multiple env vars and extract them in readable format
- Compare podman inspect output of running vs stopped container
- Write a script that checks all containers for non-zero exit codes automatically

