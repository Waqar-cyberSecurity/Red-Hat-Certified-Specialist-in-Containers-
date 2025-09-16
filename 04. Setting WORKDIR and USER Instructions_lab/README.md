# Lab 4: Setting WORKDIR and USER Instructions
## 🎯 Objectives

- Learn how to set the working directory (WORKDIR) in a Containerfile

- Create and switch to a non-root user (USER) for improved container security

- Verify container execution as a non-root user

- Understand the security implications of running containers as non-root

## 📌 Prerequisites

- Basic understanding of container concepts

- Podman or Docker installed (Podman recommended)

- Text editor (Vim, Nano, VS Code)

- Linux/Unix-based system (or WSL2 for Windows users)

---

## ⚙️ Lab Setup

Verify Podman installation:
```
podman --version
```

✅ Expected Output: version number (e.g., 4.0.2)

Create a lab directory:
```
mkdir container-security-lab && cd container-security-lab
```
---

# 📝 Task 1: Using WORKDIR in Containerfile
## 🔹 Subtask 1.1: Create Basic Containerfile
- dockerfile
```
FROM registry.access.redhat.com/ubi9/ubi-minimal
LABEL maintainer="Your Name <your.email@example.com>"
```
## 🔹 Subtask 1.2: Implement WORKDIR

Add WORKDIR instruction:
- dockerfile
```
WORKDIR /app
RUN pwd > /tmp/workdir.log && whoami >> /tmp/workdir.log
```

Build the image:
```
podman build -t workdir-demo .
```

Verify:
```
podman run --rm workdir-demo cat /tmp/workdir.log
```

✅ Expected Output:
```
/app
root
```

📌 Key Concept: WORKDIR sets the default directory for subsequent instructions & container runtime.

---

# 📝 Task 2: Creating a Non-Root User
## 🔹 Subtask 2.1: Add User Creation

Modify Containerfile:
- dockerfile
```
RUN microdnf install shadow-utils && \
    useradd -u 1001 -d /home/appuser -m appuser && \
    chown -R appuser:appuser /app
```
## 🔹 Subtask 2.2: Switch User with USER

- dockerfile
```
USER appuser
RUN whoami >> /tmp/user.log && ls -ld /app >> /tmp/user.log
```

Rebuild and run:
```
podman build -t nonroot-demo .
```
```
podman run --rm nonroot-demo cat /tmp/user.log
```

✅ Expected Output:
```
appuser
drwxr-xr-x 2 appuser appuser 6 Mar  1 12:00 /app
```

📌 Security Note: Running as non-root limits damage in case of container breach.

---

# 📝 Task 3: Security Validation
## 🔹 Subtask 3.1: Test Privileged Operation
```
podman run --rm nonroot-demo touch /sys/kernel/profiling
```

✅ Expected Outcome: Permission denied

## 🔹 Subtask 3.2: Verify User Context
```
podman run -d --name testuser nonroot-demo sleep 300
```
```
podman exec testuser ps -ef
```

✅ Expected Output: All processes show appuser as owner.

---

 📌 Troubleshooting:

- Ensure user has exec permissions

- Check ownership of filesystem paths

---

# 📝 Task 4: Security Implications Analysis
## 🔹 Subtask 4.1: Risk Comparison

Run as root:
```
podman run --rm --user root workdir-demo whoami
```

Run as non-root:
```
podman run --rm nonroot-demo whoami
```

📌 Key Findings:

- Root containers can modify system files

- Non-root containers are restricted

- Follow Principle of Least Privilege → always run with minimal permissions

---

# ✅ Conclusion

In this lab, you achieved:

- Configured WORKDIR for consistent paths

- Created and switched to a non-root user

- Validated security restrictions

---

## 📌 Best Practices:

- Always create & use non-root users in containers

- Combine with filesystem permissions

- Explore podman unshare for advanced namespaces

---

## 🚀 Next Steps

- Try Podman’s --userns=keep-id for host-user matching

- Implement read-only FS with --read-only

- Review Red Hat’s Container Hardening Guide

---

## 🧹 Cleanup
```
podman rmi workdir-demo nonroot-demo
rm -rf container-security-lab
```

---

 # Hands-On Expected Output:

<img src="lab 4.png" alt="GitHub Banner" width="100%" />

---

<img src="lab 4.1 .png" alt="GitHub Banner" width="100%" />

---

<img src="lab 4..2.png" alt="GitHub Banner" width="100%" />

---

<img src="lab 4.2 .png" alt="GitHub Banner" width="100%" />
