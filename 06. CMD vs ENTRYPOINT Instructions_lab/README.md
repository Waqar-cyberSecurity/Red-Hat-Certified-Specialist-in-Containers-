# Lab 6: CMD vs ENTRYPOINT Instructions
## 🎯 Objectives
By the end of this lab, you will be able to:

- Understand the difference between CMD and ENTRYPOINT instructions in container images

- Implement both instructions in Containerfiles

- Combine ENTRYPOINT with CMD for default arguments

- Test command overriding at runtime

## 📌 Prerequisites
- Podman or Docker installed (Podman recommended for OpenShift alignment)

- Basic Linux command-line knowledge

- Text editor (vim/nano/VS Code)

- Internet access to pull base images

---

## ⚙️ Lab Setup
- Verify Podman installation:
```
podman --version
```
Create a lab directory:

```
mkdir cmd-entrypoint-lab && cd cmd-entrypoint-lab
```
---

# 📝 Task 1: Understanding CMD Instruction
## 🔹 Subtask 1.1: Basic CMD Implementation
Create Containerfile.cmd:

dockerfile
```
FROM alpine:latest
CMD ["echo", "Hello from CMD"]
```
Build and run:

```
podman build -t cmd-demo -f Containerfile.cmd
```
```
podman run cmd-demo
```
✅ Expected Output:

csharp
```
Hello from CMD
```

## 🔹 Subtask 1.2: Overriding CMD
Run with command override:

```
podman run cmd-demo echo "Overridden command"
```
✅ Expected Output:

```
Overridden command
```
📌 Key Concept: CMD provides default commands that can be easily overridden at runtime.

---

# 📝 Task 2: Understanding ENTRYPOINT Instruction
## 🔹 Subtask 2.1: Basic ENTRYPOINT Implementation
Create Containerfile.entrypoint:

dockerfile
```
FROM alpine:latest
ENTRYPOINT ["echo", "Hello from ENTRYPOINT"]
```
Build and run:

```
podman build -t entrypoint-demo -f Containerfile.entrypoint
```
```
podman run entrypoint-demo
```
✅ Expected Output:

csharp
```
Hello from ENTRYPOINT
```
## 🔹 Subtask 2.2: Appending to ENTRYPOINT
Run with additional arguments:

```
podman run entrypoint-demo "with appended text"
```
✅ Expected Output:

vbnet
```
Hello from ENTRYPOINT with appended text
```
📌 Key Concept: ENTRYPOINT makes the container behave like an executable, with runtime arguments appended.

---

# 📝 Task 3: Combining ENTRYPOINT and CMD
## 🔹 Subtask 3.1: ENTRYPOINT with CMD Defaults
Create Containerfile.combined:

dockerfile
```
FROM alpine:latest
ENTRYPOINT ["echo"]
CMD ["Default message"]
```
Build and run:

```
podman build -t combined-demo -f Containerfile.combined
```
```
podman run combined-demo
```
✅ Expected Output:

rust
```
Default message
```
## 🔹 Subtask 3.2: Overriding CMD in Combined Setup
Run with custom message:

```
podman run combined-demo "Custom message"
```
✅ Expected Output:

vbnet
```
Custom message
```
📌 Key Concept:

- ENTRYPOINT → defines the executable

- CMD → provides default arguments

---

# 📝 Task 4: Advanced Usage Patterns
## 🔹 Subtask 4.1: Shell Script as ENTRYPOINT
Create entrypoint.sh:

```
#!/bin/sh
echo "Starting container with arguments: $@"
exec "$@"
```
Make it executable:

```
chmod +x entrypoint.sh
```
Create Containerfile.script:

dockerfile
```
FROM alpine:latest
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["echo", "Default execution"]
```
Build and run:

```
podman build -t script-demo -f Containerfile.script
```
```
podman run script-demo
```
## 🔹 Subtask 4.2: Full Command Override
Override ENTRYPOINT at runtime:

```
podman run --entrypoint /bin/ls script-demo -l /
```
✅ Expected Output: Directory listing of /

---

## 🛠 Troubleshooting Tips
- If commands don’t execute: verify JSON array formatting (["cmd", "arg"])

- If script errors:

- Ensure shebang (#!/bin/sh) exists

- Ensure script has LF line endings (not CRLF)

- Verify script permissions: chmod +x entrypoint.sh

---

# ✅ Conclusion
In this lab, you learned:

- CMD → provides default commands (easy to override)

- ENTRYPOINT → makes containers act like executables

- ENTRYPOINT + CMD → powerful combo for flexible defaults

- Runtime overrides work differently for CMD vs ENTRYPOINT

## 🧪 Final Verification
List your images:

```
podman images
```
Run a final test:

```
podman run --rm combined-demo "Lab completed successfully!"
```
## 🧹 Cleanup
```
podman rmi cmd-demo entrypoint-demo combined-demo script-demo
```
## 🚀 Next Steps

- Experiment with multi-stage builds

- Explore how these instructions behave in Kubernetes Pods

---
