# Lab 1: Understanding the FROM Instruction
## 🎯 Objectives
By the end of this lab, you will be able to:

- Understand the purpose and importance of the FROM instruction in containerization

- Select and pin appropriate base images for reproducible builds

- Create a basic Containerfile using the FROM instruction

## 📌 Prerequisites
- Basic Linux command line knowledge

- Podman installed (version 3.0+ recommended)

- Internet access to pull container images

---

## ⚙️ Setup Requirements

Verify Podman installation:

```
podman --version
```
Expected output:

nginx
```
podman version 3.4.4
```
Create working directory:

```
mkdir from-lab && cd from-lab
```
---

# 📝 Task 1: Understanding the FROM Instruction
## 🔹 Subtask 1.1: What is the FROM Instruction?
- First instruction in a Containerfile/Dockerfile

- Specifies the base image for subsequent instructions

- Defines the starting point for your container build

- Can be any valid image from a registry or local system

## 🔹 Subtask 1.2: Importance of Base Images
- Size → Small (e.g., Alpine) vs. full (e.g., Ubuntu)

- Security → Official vs. community images

- Maintenance → Regular updates & security patches

- Compatibility → OS & architecture requirements

---

# 📝 Task 2: Selecting and Pinning a Base Image
## 🔹 Subtask 2.1: Finding Official Images
Search for UBI 8 images:

```
podman search registry.access.redhat.com/ubi8
```
Inspect image tags (requires skopeo):

```
skopeo inspect docker://registry.access.redhat.com/ubi8/ubi:latest
```
Install skopeo if needed:

```
sudo dnf install skopeo
```
## 🔹 Subtask 2.2: Pinning Image Versions
Best practices:

- Always specify a tag (avoid latest)

- Use digest for maximum reproducibility

Example:

```
podman pull registry.access.redhat.com/ubi8/ubi:8.7
```
---

# 📝 Task 3: Writing a Simple Containerfile
## 🔹 Subtask 3.1: Create Containerfile
```
touch Containerfile
```
Add the following:

dockerfile
```

# Use a pinned UBI 8 image
FROM registry.access.redhat.com/ubi8/ubi:8.7

# Set a label
LABEL maintainer="your.email@example.com"

# Run a simple command
RUN echo "Base image successfully set up" > /tmp/status.txt
```

## 🔹 Subtask 3.2: Build and Verify Image
Build the image:

```
podman build -t my-base-image .
```
Check image:

```
podman images
```
Run and verify:

```
podman run --rm my-base-image cat /tmp/status.txt
```
Expected output:
```
arduino

Base image successfully set up
```

---

## 🛠️ Troubleshooting

- Image not found: Check name, tag, internet, registry permissions

- Build failures: Check typos, test each instruction, use --no-cache

- Permission issues: Run as root or rootless, check SELinux

---

# ✅ Conclusion
 In this lab, you learned how to:

- Use the FROM instruction in container builds

- Select & pin base images for reproducibility

- Write and build a simple Containerfile

## 🚀 Next Steps
- Try different base images (Alpine, Debian, CentOS)

- Build multi-stage containers

- Explore image layering with:

```
podman history my-base-image
```
---

## 🧹 Cleanup (Optional)
Remove created image:

```
podman rmi my-base-image
```
Remove all unused images:

```
podman image prune -a
```
---

# Hands-On Expected Output:

<img src="lab 1 .png" alt="GitHub Banner" width="100%" />

---

<img src="lab1.1 .png" alt="GitHub Banner" width="100%" />

---
