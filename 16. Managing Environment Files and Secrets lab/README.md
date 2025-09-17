# Lab 16: Managing Environment Files and Secrets with Podman

## 🎯 Objectives
By the end of this lab, you will be able to:
- Create and use `.env` files to manage environment variables  
- Pass environment variables to containers from files  
- Create and manage Podman secrets for sensitive data  
- Reference secrets in **Podman Compose** YAML files  

---

## 🛠 Prerequisites
- Podman installed (v3.0+ recommended)  
- Basic familiarity with Linux command line  
- Text editor (vim/nano/VSCode)  
- Podman Compose installed:
```bash
pip install podman-compose
```
---

## ⚙️ Lab Setup
Create a working directory:

```
mkdir podman-secrets-lab && cd podman-secrets-lab
```
---

##  🚀 Tasks
# Task 1: Working with Environment Files
## Subtask 1.1: Create .env File
Create a .env file:

```
cat <<EOF > .env
APP_NAME=MySecureApp
DB_USER=admin
DB_PASS=SuperSecret123!
EOF
```
Verify contents:

```
cat .env
```
✅ Expected:

ini
```
APP_NAME=MySecureApp
DB_USER=admin
DB_PASS=SuperSecret123!
```
## Subtask 1.2: Use Environment Variables in Container
Run a container with env file:

```
podman run --rm --env-file=.env alpine env | grep -E 'APP_NAME|DB_'
```
✅ Expected:

ini
```
APP_NAME=MySecureApp
DB_USER=admin
DB_PASS=SuperSecret123!
```
👉 Key Concept: --env-file loads environment variables in bulk.

---

# Task 2: Working with Podman Secrets
## Subtask 2.1: Create a Secret
Create a secret file:

```
echo "TopSecretPassword" > db_password.txt
```
Add as a Podman secret:

```
podman secret create db_password_secret db_password.txt
```
List secrets:

```
podman secret ls
```
✅ Example output:

nginx
```
ID        NAME                DRIVER    CREATED          UPDATED
xxxxxx    db_password_secret   file      2s ago           2s ago
```
## Subtask 2.2: Use Secret in a Container
Run a container with secret:

```
podman run --rm --secret=db_password_secret alpine cat /run/secrets/db_password_secret
```
✅ Expected:

nginx
```
TopSecretPassword
```
⚠️ Troubleshooting: If permission denied → run as root or use --privileged.

---

# Task 3: Podman Compose Integration
## Subtask 3.1: Create docker-compose.yml
```
cat <<EOF > docker-compose.yml
version: '3.8'
services:
  webapp:
    image: alpine
    env_file: .env
    secrets:
      - db_password_secret
    command: sh -c "echo \$APP_NAME && cat /run/secrets/db_password_secret"

secrets:
  db_password_secret:
    external: true
EOF
```
## Subtask 3.2: Deploy with Podman Compose
Run compose:

```
podman-compose up
```
✅ Expected:

nginx
```
webapp_1  | MySecureApp
webapp_1  | TopSecretPassword
```
👉 external: true means the secret is managed outside this YAML file.

---

## 🧹 Cleanup
```
podman secret rm db_password_secret

cd .. && rm -rf podman-secrets-lab
```

---

# ✅ Conclusion
In this lab, you learned how to:

- Manage environment variables using .env files

- Create and use Podman secrets for sensitive data

- Integrate both .env and secrets with Podman Compose

These skills are crucial for secure container deployment in production.

---

## 📚 Additional Resources
- [Podman Secrets Documentation](https://docs.podman.io/en/latest/markdown/podman-secret.1.html)

- [12 Factor App Config Best Practices](https://12factor.net/config)

---
