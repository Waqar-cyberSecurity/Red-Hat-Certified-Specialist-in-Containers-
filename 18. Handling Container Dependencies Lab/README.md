# 🚀 Lab 18: Handling Container Dependencies

## 🎯 Objectives

- Implement depends_on in Docker Compose files to manage service startup order
- Write simple health check scripts for container services
- Implement retry logic in container entrypoints
- Test service connectivity between dependent containers

## Prerequisites

- Basic understanding of Docker/Podman and container concepts
- Docker or Podman installed (Podman preferred for OpenShift alignment)
- Docker Compose or Podman Compose installed
- Text editor (VS Code, nano, vim, etc.)
- Terminal/command line access

---

## Lab Setup
1. Create a new directory for this lab:
```
mkdir container-dependencies-lab && cd container-dependencies-lab
```
2. Verify your Podman/Docker installation:
```
podman --version
podman-compose --version
```

---

# 🛠️ Task 1: depends_on + Healthcheck
- docker-compose.yml

yaml
```
version: '3.8'
services:
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_PASSWORD: example
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  web:
    image: nginx:alpine
    ports:
      - "8080:80"
    depends_on:
      db:
        condition: service_healthy
```
👉 Run:

```
podman-compose up -d
podman ps
```
✅ Result: web sirf tabhi start karega jab db healthy ho.

---


# 🛠️ Task 2: Custom Healthcheck
- healthcheck.sh

sh
```
#!/bin/sh
if nc -z db 5432; then
  exit 0
else
  exit 1
fi
```
```
chmod +x healthcheck.sh
```
- docker-compose.yml (add app service):

yaml
```
  app:
    image: python:3.9-alpine
    volumes:
      - ./healthcheck.sh:/healthcheck.sh
    healthcheck:
      test: ["CMD", "/healthcheck.sh"]
      interval: 10s
      timeout: 5s
      retries: 3
```

---

# 🛠️ Task 3: Retry Logic in Entrypoint
- entrypoint.sh

sh
```
#!/bin/sh
max_retries=5
retry_delay=5

for i in $(seq 1 $max_retries); do
  if nc -z db 5432; then
    echo "Database is ready!"
    exec "$@"
    exit 0
  else
    echo "Waiting for DB... Attempt $i/$max_retries"
    sleep $retry_delay
  fi
done

echo "DB not available after $max_retries attempts"
exit 1
```
```
chmod +x entrypoint.sh
```

- Dockerfile

Dockerfile
```
FROM python:3.9-alpine
WORKDIR /app
COPY entrypoint.sh .
RUN chmod +x entrypoint.sh
ENTRYPOINT ["./entrypoint.sh"]
CMD ["python", "app.py"]
```
---

# 🛠️ Task 4: Test Service Connectivity
- app.py

python
```
import os, psycopg2

def connect_db():
    try:
        conn = psycopg2.connect(
            host="db",
            database="postgres",
            user="postgres",
            password=os.getenv("POSTGRES_PASSWORD")
        )
        print("✅ Connected to DB!")
        conn.close()
    except Exception as e:
        print(f"❌ Connection failed: {e}")

if __name__ == "__main__":
    connect_db()
```

- docker-compose.yml update:

yaml
```
  app:
    build: .
    depends_on:
      db:
        condition: service_healthy
    environment:
      POSTGRES_PASSWORD: example
```
👉 Run and test:

```
podman-compose up --build
```
✅ Result: app waits for db, then connects successfully.

---

# Troubleshooting Tips
If containers fail to start:

Check logs:
``` 
podman-compose logs
```
Verify health checks:
```
 podman inspect --format='{{.State.Health.Status}}' container_name
```
2. If health checks fail:

- Adjust intervals and retries in compose file
- Test health check scripts manually

3. For connection issues:

- Verify network connectivity between containers
- Check service names match in configurations

---

# ✅ Conclusion
In this lab, you've successfully:

- Implemented container dependency management using depends_on with health checks
- Created custom health check scripts to verify service readiness
- Developed robust entrypoint scripts with retry logic
- Tested service connectivity between dependent containers


These skills are essential for building reliable containerized applications where services must start in a specific order and handle dependencies gracefully.

## 🧹 Cleanup
```
podman-compose down
podman system prune -f
```
