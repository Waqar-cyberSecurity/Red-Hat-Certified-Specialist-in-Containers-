# Lab 7: Parameterized ENTRYPOINT Scripts
## 🎯 Objectives
By the end of this lab, you will be able to:

- Understand the role of ENTRYPOINT in container initialization

- Create and test ENTRYPOINT scripts that handle environment-specific logic

- Implement parameter passing between CMD and ENTRYPOINT

- Distinguish between development and production modes in container execution

## 📌 Prerequisites
- Podman or Docker installed (Podman recommended for OpenShift alignment)

- Basic Linux command-line knowledge

- Familiarity with shell scripting

- Text editor (vim, nano, VS Code)

---

## ⚙️ Setup Requirements
Verify Podman installation:

```
podman --version
```
Create a working directory:

```
mkdir entrypoint-lab && cd entrypoint-lab
```
---

# 📝 Task 1: Create a Basic ENTRYPOINT Script
## 🔹 Subtask 1.1: Write the Shell Script
Create a file named entrypoint.sh:

```
#!/bin/bash

echo "Container starting..."
echo "Executing as user: $(whoami)"
echo "Current directory: $(pwd)"
```
## 🔹 Subtask 1.2: Make the Script Executable
```
chmod +x entrypoint.sh
```
## 🔹 Subtask 1.3: Create a Dockerfile
dockerfile
```
FROM alpine:latest
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```
## 🔹 Subtask 1.4: Build and Run the Container
```
podman build -t entrypoint-demo .
```
```
podman run entrypoint-demo
```
✅ Expected Output:

sql
```
Container starting...
Executing as user: root
Current directory: /
```
---

# 📝 Task 2: Implement Parameter Handling
## 🔹 Subtask 2.1: Modify the Entrypoint Script
Update entrypoint.sh:

```
#!/bin/bash

echo "Container starting with arguments: $@"
echo "First argument: ${1:-none}"
echo "Second argument: ${2:-none}"

# Execute the command passed from CMD
exec "$@"
```
## 🔹 Subtask 2.2: Update the Dockerfile
dockerfile
```
FROM alpine:latest
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["echo", "Default command executed"]
```
## 🔹 Subtask 2.3: Test Different Scenarios
Run with default CMD:

```
podman run entrypoint-demo
```
✅ Output:

sql
```
Container starting with arguments: echo Default command executed
First argument: echo
Second argument: Default command executed
Default command executed
```

Override CMD at runtime:

```
podman run entrypoint-demo ls -l /
```
---

# 📝 Task 3: Environment-Specific Logic
## 🔹 Subtask 3.1: Create Mode Detection Script
Update entrypoint.sh:

```
#!/bin/bash

# Environment detection
if [ "$ENV_MODE" = "production" ]; then
    echo "PRODUCTION MODE: Strict settings applied"
    # Add production-specific logic here
elif [ "$ENV_MODE" = "development" ]; then
    echo "DEVELOPMENT MODE: Debug features enabled"
    # Add development-specific logic here
else
    echo "No ENV_MODE specified, running in default mode"
fi

exec "$@"
```
##  🔹 Subtask 3.2: Update Dockerfile for Environment Variables
dockerfile
```
FROM alpine:latest
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["echo", "Running container"]
```
## 🔹 Subtask 3.3: Test Different Modes
Development mode:

```
podman run -e ENV_MODE=development entrypoint-demo
```
Production mode:

```
podman run -e ENV_MODE=production entrypoint-demo
```
Default mode:

```
podman run entrypoint-demo
```
---

# 📝 Task 4: Advanced Parameter Handling
## 🔹 Subtask 4.1: Create Configuration Script
Update entrypoint.sh:

```
#!/bin/bash

# Process arguments
case "$1" in
    start)
        echo "Starting application with config: ${2:-default}"
        ;;
    stop)
        echo "Stopping application gracefully"
        ;;
    *)
        echo "Usage: $0 {start|stop} [config]"
        exit 1
esac
```

## 🔹 Subtask 4.2: Test Command Patterns
```
podman run entrypoint-demo start production
podman run entrypoint-demo stop
podman run entrypoint-demo invalid
```
---

## 🛠 Troubleshooting Tips
- Permission denied → chmod +x entrypoint.sh

- ENV not recognized → verify -e flag and variable names

- Exec format error →

- Ensure correct shebang (#!/bin/bash)

- Use LF line endings (dos2unix entrypoint.sh if needed)

---

# ✅ Conclusion
In this lab, you learned how to:

- Create parameterized ENTRYPOINT scripts for containers

- Handle environment variables for different execution modes

- Process command arguments passed to containers

- Implement production vs development differentiation logic

These skills are essential for building flexible container images that adapt to runtime environments — a key requirement in modern container orchestration (e.g., OpenShift, Kubernetes).

---

## 🚀 Next Steps
- Experiment with complex environment detection

- Integrate configuration file processing

- Explore multi-stage builds with ENTRYPOINT scripts

- Test these concepts in OpenShift environments
