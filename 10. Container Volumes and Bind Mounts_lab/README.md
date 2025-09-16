# Lab 10: Container Volumes and Bind Mounts

## 🎯 Objectives
- Understand different volume types
- Create and manage named volumes
- Implement bind mounts with SELinux `:Z` option
- Configure host directory permissions
- Verify persistence across container lifecycles

## 🛠 Prerequisites
- Podman (v3.0+ recommended)
- Basic Linux CLI knowledge
- SELinux enabled system (default on RHEL-based systems)
- `sudo` privileges

---

## 🚀 Lab Steps

### Task 1: Named Volumes

**Create a named volume**
```bash
podman volume create mydata_volume
podman volume ls
```
Use the volume in a container

```
podman run -d --name volume_test -v mydata_volume:/data docker.io/library/alpine sleep infinity
```
```
podman exec volume_test ls /data
```
Test persistence

```
podman exec volume_test sh -c "echo 'Persistent Data' > /data/testfile"
podman stop volume_test && podman rm volume_test
podman run --rm -v mydata_volume:/data docker.io/library/alpine cat /data/testfile

# Expected: Persistent Data
```

## Task 2: Bind Mounts with SELinux
Create host directory

```
mkdir ~/container_data
echo "Host file content" > ~/container_data/hostfile.txt
```
Run with :Z option

```
podman run --rm -v ~/container_data:/container_data:Z docker.io/library/alpine cat /container_data/hostfile.txt
# Expected: Host file content
```

Verify SELinux context

```
ls -Z ~/container_data/hostfile.txt
```
---

# Task 3: Permissions

Restricted directory

```
sudo mkdir /restricted_data
sudo chmod 700 /restricted_data
sudo chown root:root /restricted_data
```
Test access (should fail)

```
podman run --rm -v /restricted_data:/data alpine touch /data/testfile
# Expected: Permission denied
```
Fix permissions and SELinux

```
sudo chmod 755 /restricted_data
sudo chcon -t container_file_t /restricted_data
podman run --rm -v /restricted_data:/data:Z alpine touch /data/testfile
ls -lZ /restricted_data
```
---

# Task 4: Persistence Test
Create container with both mounts

```
podman run -d --name persist_test \
  -v mydata_volume:/data \
  -v ~/container_data:/container_data:Z \
  docker.io/library/alpine sleep infinity
```
Write data

```
podman exec persist_test sh -c "echo 'Named Volume Data' >> /data/named.txt"
podman exec persist_test sh -c "echo 'Bind Mount Data' >> /container_data/bind.txt"
```
Verify persistence

```
podman stop persist_test && podman rm persist_test
podman run --rm -v mydata_volume:/data alpine cat /data/named.txt
cat ~/container_data/bind.txt
# Expected:
# Named Volume Data
# Bind Mount Data
```
## 🧹 Cleanup
```
podman volume rm mydata_volume
podman rm -f $(podman ps -aq)
rm -rf ~/container_data
sudo rm -rf /restricted_data
```
---

# 📚 References
- man podman-volume
- man container_selinux
- [Red Hat Container Storage Guide](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html-single/building_running_and_managing_containers/index)
