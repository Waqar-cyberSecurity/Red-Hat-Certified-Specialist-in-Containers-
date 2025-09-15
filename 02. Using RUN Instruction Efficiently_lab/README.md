# Lab 2: Using RUN Instruction Efficiently
## 🎯 Objectives

By the end of this lab, you will be able to:

- Use the RUN instruction in both shell and exec forms.

- Combine package installations and cache cleanup in a single RUN instruction to minimize image layers.

- Build and analyze Docker/Podman images to observe layer optimization.

## 📌 Prerequisites

- Basic familiarity with Docker/Podman

- A Linux-based system (Ubuntu/CentOS recommended) with Podman/Docker installed

- Internet access to download packages

---

# 📝 Task 1: Understanding RUN Instruction Forms
## 🔹 Subtask 1.1: RUN in Shell Form

The shell form executes commands in a shell (/bin/sh -c by default).

Create a Dockerfile:
```
FROM alpine:latest
RUN apk add --no-cache curl
```

Build the image:
```
podman build -t run-lab-shell .
```

✅ Expected Outcome:
The curl package is installed in a new layer.

## 🔹 Subtask 1.2: RUN in Exec Form

The exec form (RUN ["executable", "param1", "param2"]) avoids shell processing.

Modify the Dockerfile:
```
FROM alpine:latest
RUN ["/bin/sh", "-c", "apk add --no-cache curl"]
```

Build the image:
```
podman build -t run-lab-exec .
```

📌 Key Concept: Exec form helps avoid shell-specific syntax issues.

---

# 📝 Task 2: Combining Commands to Minimize Layers
## 🔹 Subtask 2.1: Inefficient Multi-Layer Approach

Each RUN creates a new layer, increasing image size.

Example Dockerfile:
```
FROM ubuntu:latest
RUN apt-get update
RUN apt-get install -y nginx
RUN rm -rf /var/lib/apt/lists/*
```

⚠️ Problem: Three layers are created, and unnecessary cache files remain.

## 🔹 Subtask 2.2: Optimized Single-Layer Approach

Combine commands using && and clean up in one RUN.

Optimized Dockerfile:
```
FROM ubuntu:latest
RUN apt-get update && \
    apt-get install -y nginx && \
    rm -rf /var/lib/apt/lists/*
```

Build and compare:
```
podman build -t optimized-nginx .
```
```
podman images | grep -E 'optimized-nginx|run-lab-shell'
```

✅ Expected Outcome:
Optimized image has fewer layers and smaller size.

---

# 📝 Task 3: Verifying Layer Reduction
## 🔹 Subtask 3.1: Inspecting Image Layers

Analyze layers with:
```
podman history optimized-nginx
```

Output Analysis: Only one RUN layer appears for the combined commands.

## 🔹 Subtask 3.2: Comparing Image Sizes

Compare sizes:
```
podman images
```

📌 Key Takeaway:
Fewer layers = smaller image size and faster builds.

---

## 🛠️ Troubleshooting Tips

- Permission Issues: Use --privileged flag if needed

- Cache Problems: Force clean build if packages don’t update:
```
podman build --no-cache -t optimized-nginx .
```
---

# ✅ Conclusion

In this lab, you:

- Practiced using RUN in shell and exec forms

- Combined commands to minimize layers & reduce image size

- Verified optimization using podman history

---

## 🚀 Next Steps

- Apply these techniques in your own Dockerfiles for efficient builds

- Explore other instructions (CMD, ENTRYPOINT)

- Try optimizing multi-stage builds

---
