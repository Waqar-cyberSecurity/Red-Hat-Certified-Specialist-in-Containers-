# Lab 17: Running Multi-Container Applications

## 🎯 Objectives
By the end of this lab, you will be able to:
- Define and deploy multi-container applications using **podman-compose**  
- Configure volumes, networks, and secrets for multi-container applications  
- Deploy applications using **Kubernetes YAML** with `podman play kube`  

---

## 🛠 Prerequisites
- Podman installed (version 3.0+)  
- `podman-compose` installed:  
  
```bash
  pip install podman-compose
```

- Basic understanding of Docker/Podman concepts

- Familiarity with YAML syntax

---

## ⚙️ Lab Setup
Start Podman service:

```
systemctl start podman
```
Verify installations:

```
podman --version
podman-compose --version
```

---

# 🚀 Task 1: Create and Deploy a Multi-Container Application with podman-compose
## Subtask 1.1: Create docker-compose.yml
Create a project directory:

```
mkdir multi-container-lab && cd multi-container-lab
```
Create docker-compose.yml:

yaml
```
version: '3.8'

services:
  web:
    image: docker.io/python:3.9
    command: python -m http.server 8000
    ports:
      - "8000:8000"
    volumes:
      - ./app:/app
    depends_on:
      - redis
      - db
    networks:
      - app-network

  redis:
    image: docker.io/redis:alpine
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - app-network

  db:
    image: docker.io/postgres:13-alpine
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - app-network

volumes:
  redis-data:
  postgres-data:

networks:
  app-network:
    driver: bridge
```

## Subtask 1.2: Deploy the Stack
Run:

```
podman-compose up -d
```
Verify:

```
podman ps
```
Test web service:

```
curl http://localhost:8000
```
✅ Expected: Python HTTP server response

---

# Task 2: Add Secrets Management
## Subtask 2.1: Create Secrets
Create DB password secret:

```
echo "supersecret" | podman secret create db_password -
```
Update docker-compose.yml for DB:

yaml
```
services:
  db:
    image: docker.io/postgres:13-alpine
    secrets:
      - db_password
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password

secrets:
  db_password:
    external: true
```
Redeploy:

```
podman-compose down
```
```
podman-compose up -d
```
⚠️ If permission errors occur:

```
sudo setsebool -P container_manage_cgroup true
```
---

# Task 3: Deploy with Kubernetes YAML
## Subtask 3.1: Generate Kubernetes YAML
Generate manifests:

```
podman kube generate --service -f k8s-deployment.yaml web redis db
```
Review k8s-deployment.yaml

## Subtask 3.2: Deploy with Podman
```
podman play kube k8s-deployment.yaml
```
Verify:

```
podman pod ps
podman ps
```
---

# ✅ Conclusion
In this lab, you learned to:

- Define multi-container apps with docker-compose.yml

- Configure volumes, networks, and secrets

- Deploy stacks with podman-compose

- Generate & deploy Kubernetes YAML using podman play kube

These skills are crucial for managing complex containerized applications in dev and production environments.

---

## 🧹 Cleanup
```
podman-compose down
podman pod rm -a -f
podman rm -a -f
podman volume prune
podman secret rm db_password
```
---

## 📚 Additional Resources

- [Podman Documentation](https://docs.podman.io/en/latest/)
- [Compose Specification](https://compose-spec.io/)
- [Kubernetes YAML Reference](https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/)
