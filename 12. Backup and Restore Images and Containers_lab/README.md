# Lab 12: Backup and Restore Images and Containers with Podman

## 🎯 Objectives
- Backup & restore images using `podman save` and `podman load`
- Create new images from running containers with `podman commit`
- Understand practical use cases & limitations

---

## 🛠 Prerequisites
- Linux system with Podman (RHEL 8+, Fedora recommended)
- Podman version `3.x.x` or higher
- Internet access (for pulling images)
- 1GB+ free disk space
- Familiarity with Podman basics

---

## ⚙️ Lab Setup
Verify Podman installation:
```bash
podman --version
# Expected: podman version 3.x.x
```
Pull sample image:
```
podman pull docker.io/library/alpine:latest
```
##🚀 Tasks
# Task 1: Save Image to Tarball
List images

```
podman images
```
Save image

```
podman save -o alpine_backup.tar docker.io/library/alpine:latest
```
Verify tarball

```
ls -lh alpine_backup.tar
file alpine_backup.tar
```
✅ Expected: ~3MB .tar file

---

# Task 2: Load Image from Tarball
Remove original image
```
podman rmi docker.io/library/alpine:latest
```
Restore from tarball

```
podman load -i alpine_backup.tar
```
Verify

```
podman images
```
🔑 Key: Image name & tags are preserved.

---

# Task 3: Commit Container to New Image
Run interactive container

```
podman run -it --name myalpine docker.io/library/alpine:latest /bin/sh
```
Inside container

sh
```
touch /testfile
echo "Lab 12" > /testfile
exit
```
Commit to new image
```
podman commit myalpine my_custom_alpine:v1
```
Verify

```
podman images
```
```
podman run --rm my_custom_alpine:v1 cat /testfile
```


💡 Use Case: Save container state, debugging, custom image creation.

---

# Task 4: Limitations
- Tarball Portability

- Architecture specific (x86_64 tarball won’t run on ARM)

- Commit Limitations

- Doesn’t capture running processes, volumes, or networks

- Disk Usage
```
podman system df
```
---

# ✅ Conclusion
In this lab, you:

- Saved images with podman save

- Restored them with podman load

- Built custom images using podman commit

- Learned limitations of backups & commits



## 🌍 Real-World Applications

- Migrating images to air-gapped systems

- Creating golden images

- Disaster recovery strategies

## 📚 Next Steps
- Try with larger images (Ubuntu, CentOS)

- Test multi-architecture handling

- Explore podman export & podman import for container filesystem backup

---

## 🧹 Cleanup
```
podman rm -a
podman rmi -a
rm alpine_backup.tar
```
## 📝 Knowledge Check
1. Difference between podman save and podman export?

- save → works on images (keeps layers + metadata)

- export → works on containers (filesystem only)

2. How to verify tarball integrity?

```
sha256sum alpine_backup.tar
```
3. When to use commit over Dockerfile build?

- When preserving runtime state/configs or when Dockerfile isn’t available.

