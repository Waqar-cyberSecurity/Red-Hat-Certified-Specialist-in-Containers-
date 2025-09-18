# Lab 20: Case Study – Build and Deploy a Secure Flask Microservice
## Objectives
By the end of this lab, you will be able to:

- Containerize a Flask application using Podman.

- Create and manage a PostgreSQL container with persistent storage.

- Deploy a multi-container application stack using Podman Compose.

- Implement security best practices including image scanning and signing.

- Test application recovery and log management.

## Prerequisites
- Linux system with Podman installed:

```
sudo dnf install podman podman-compose
```
- Basic knowledge of Python and Flask.

- Familiarity with PostgreSQL and container concepts.

- Internet access for pulling container images.

---

## Lab Setup
1. Create a working directory:

```
mkdir flask-microservice-lab && cd flask-microservice-lab
```

2. Verify Podman installation:

```
podman --version
podman-compose --version
```
---

# Task 1: Create Flask Application
## Subtask 1.1: Initialize Python Environment
```
python3 -m venv venv
source venv/bin/activate
pip install flask psycopg2-binary
```
## Subtask 1.2: Create Flask App
Create app.py:

python
```
from flask import Flask, jsonify
import psycopg2
import os

app = Flask(__name__)

# Database configuration from environment
DB_HOST = os.getenv('DB_HOST', 'localhost')
DB_NAME = os.getenv('DB_NAME', 'mydb')
DB_USER = os.getenv('DB_USER', 'postgres')
DB_PASS = os.getenv('DB_PASS', 'password')

def get_db_connection():
    return psycopg2.connect(
        host=DB_HOST,
        database=DB_NAME,
        user=DB_USER,
        password=DB_PASS
    )

@app.route('/')
def hello():
    return jsonify({"status": "OK", "message": "Flask Microservice Running"})

@app.route('/data')
def get_data():
    conn = get_db_connection()
    cur = conn.cursor()
    cur.execute('SELECT version();')
    db_version = cur.fetchone()
    cur.close()
    conn.close()
    return jsonify({"database": db_version[0]})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
## Subtask 1.3: Create Requirements File
```
pip freeze > requirements.txt
```
---

# Task 2: Containerize Flask App
## Subtask 2.1: Create Containerfile
Create Containerfile:

dockerfile
```
FROM python:3.9-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

ENV FLASK_APP=app.py
EXPOSE 5000

CMD ["flask", "run", "--host=0.0.0.0"]
```

## Subtask 2.2: Build Container Image
```
podman build -t flask-app .
```
---

# Task 3: Configure PostgreSQL Container
## Subtask 3.1: Create Database Initialization Script
Create init.sql:

sql
```
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL
);
```
## Subtask 3.2: Create Secrets
```
echo "mysecretpassword" > db_password.txt
podman secret create db_password db_password.txt
```
---

# Task 4: Deploy with Podman Compose
## Subtask 4.1: Create podman-compose.yml
yaml
```
version: '3'
services:
  web:
    image: flask-app
    build: .
    ports:
      - "5000:5000"
    environment:
      - DB_HOST=db
      - DB_NAME=mydb
      - DB_USER=postgres
      - DB_PASS_FILE=/run/secrets/db_password
    secrets:
      - db_password
    depends_on:
      - db
    networks:
      - app-network

  db:
    image: postgres:13
    volumes:
      - pgdata:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD_FILE=/run/secrets/db_password
      - POSTGRES_DB=mydb
    secrets:
      - db_password
    networks:
      - app-network
    ports:
      - "5432:5432"

volumes:
  pgdata:

secrets:
  db_password:
    file: db_password.txt

networks:
  app-network:
    driver: bridge
```

## Subtask 4.2: Deploy Stack
```
podman-compose up -d
```
---

# Task 5: Security Practices
## Subtask 5.1: Scan Images
```
podman scan flask-app
podman scan postgres:13
```
## Subtask 5.2: Sign Images
```
podman trust set -t reject default
```
```
podman trust set -f ~/.ssh/id_rsa.pub docker.io
```
```
podman tag flask-app localhost/flask-app:latest
```
```
podman push localhost/flask-app:latest
```
---

# Task 6: Testing and Validation
## Subtask 6.1: Test Application
```
curl http://localhost:5000
curl http://localhost:5000/data
```
## Subtask 6.2: Check Logs
```
podman logs -f flask-microservice-lab_web_1
```
## Subtask 6.3: Test Recovery
```
podman-compose stop db
podman-compose start db
```
```
curl http://localhost:5000/data
```
---

# Conclusion
In this lab, you successfully:

- Created a secure Flask microservice with PostgreSQL backend.

- Containerized the application using Podman.

- Deployed a multi-container stack with Podman Compose.

- Implemented security best practices including image scanning and signing.

- Tested application resilience and log management.

✅ This demonstrates a production-ready pattern for deploying secure containerized microservices.

---

## Cleanup
```
podman-compose down
podman secret rm db_password
podman rmi flask-app
```

## Troubleshooting Tips
If containers fail to start:

```
podman logs <container_name>
```
- For connection issues: Verify environment variables in both containers.

- If image scanning fails: Ensure you have the latest Podman.

- For SELinux volume issues: Use :Z on volume mounts.


## Next Steps
- Add Redis for caching and performance.

- Implement health checks in your Podman Compose file.

- Set up monitoring with Prometheus and Grafana.
