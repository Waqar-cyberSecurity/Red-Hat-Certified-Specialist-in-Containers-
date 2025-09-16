# Lab 9: Using ENV and Environment Variables with Podman
## 🎯 Objectives
By the end of this lab, you will be able to:

- Define environment variables in a Containerfile

- Override environment variables at runtime using podman run -e

- Implement multi-line environment variables

- Document environment variables effectively

## 📌 Prerequisites
- Basic Linux command line knowledge

- Podman installed (version 3.0+ or later)

- A text editor (vim, nano, or VS Code)

- Internet access (for pulling base images)

---

## ⚙️ Lab Setup
Verify Podman installation:

```
podman --version
```
✅ Expected output:

nginx
```
podman version 3.4.0
```
Create a working directory:

```
mkdir env-lab && cd env-lab
```
---

# 📝 Task 1: Setting ENV in Containerfile
## 🔹 Subtask 1.1: Create a Basic Containerfile
Create Containerfile:

Dockerfile
```
FROM alpine:latest
ENV APP_NAME="MyApp" \
    APP_VERSION="1.0.0"
CMD echo "Running $APP_NAME version $APP_VERSION"
```
## 🔹 Subtask 1.2: Build and Run the Container
Build the image:

```
podman build -t env-demo .
```
Run the container:

```
podman run env-demo
```
✅ Expected output:

sql
```
Running MyApp version 1.0.0
```
---

# 📝 Task 2: Overriding ENV at Runtime
## 🔹 Subtask 2.1: Override Single Variable
```
podman run -e APP_NAME="NewApp" env-demo
```
✅ Expected output:

sql
```
Running NewApp version 1.0.0
```
## 🔹 Subtask 2.2: Override Multiple Variables
```
podman run -e APP_NAME="ProductionApp" -e APP_VERSION="2.0.0" env-demo
```
✅ Expected output:

sql
```
Running ProductionApp version 2.0.0
```
---

# 📝 Task 3: Using Multi-line ENV
## 🔹 Subtask 3.1: Create Multi-line Variables
Update Containerfile:

Dockerfile
```
FROM alpine:latest
ENV APP_NAME="MyApp" \
    APP_VERSION="1.0.0" \
    APP_DESCRIPTION="This is a multi-line \
environment variable example"
CMD echo "$APP_DESCRIPTION"
```
Rebuild and run:

```
podman build -t multiline-env .
```
```
podman run multiline-env
```
✅ Expected output:

pgsql
```
This is a multi-line environment variable example
```
---

# 📝 Task 4: Documenting Environment Variables
## 🔹 Subtask 4.1: Create Documentation
Create a README.md:

markdown
```
# Environment Variables

| Variable         | Description              | Default Value   |
|------------------|--------------------------|-----------------|
| APP_NAME         | Name of the application  | MyApp           |
| APP_VERSION      | Application version      | 1.0.0           |
| APP_DESCRIPTION  | Multi-line description   | (see Containerfile) |
```
## 🔹 Subtask 4.2: Verify Documentation
Check that your documentation matches the Containerfile definitions.

---

## 🔧 Troubleshooting Tips
- If variables don’t update → make sure you use -e before each variable.

- For multi-line variables → ensure proper backslash \ placement.

- Use podman inspect <container> to verify environment variables inside the container.

---

# ✅ Conclusion
In this lab, you learned how to:

- Define environment variables in Containerfiles

- Override them at runtime with -e

- Implement multi-line environment variables

- Document environment variables for clarity

📌 These skills are crucial for containerized application configuration and especially valuable when working with Red Hat OpenShift.

---

## 🚀 Next Steps
- Try different base images with ENV variables

- Combine ENV with command-line arguments

- Explore Podman secrets for handling sensitive data

---
