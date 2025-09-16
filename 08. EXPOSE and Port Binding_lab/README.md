# Lab 8: EXPOSE and Port Binding
## 🎯 Objectives
By the end of this lab, you will be able to:

- Understand how to document and expose ports in container images

- Learn to map container ports to host ports

- Test network connectivity between containers and hosts

- Troubleshoot common port binding issues

## 📌 Prerequisites
- Podman installed (version 3.0+ recommended)

- Basic Linux command-line knowledge

- Text editor (vim, nano, gedit)

- Network connectivity available

- curl or telnet installed for testing

---

## ⚙️ Lab Setup
Verify Podman installation:

```
podman --version
```
✅ Expected output:

nginx
```
podman version 3.x.x
```
Create a working directory:

```
mkdir portbinding-lab && cd portbinding-lab
```
---

# 📝 Task 1: Using EXPOSE in Containerfile
## 🔹 Subtask 1.1: Create a Simple Web Application
Create a Python Flask app (app.py):

```
cat > app.py <<EOF
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from the exposed container port!\n"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
EOF
```
Create requirements.txt:

```
echo "flask" > requirements.txt
```
## 🔹 Subtask 1.2: Create Containerfile with EXPOSE
```
cat > Containerfile <<EOF
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
EXPOSE 8080
CMD ["python", "app.py"]
EOF
```
💡 Key Concept:
- EXPOSE only documents which ports the container listens on — it does not publish them automatically.

---

# 📝 Task 2: Run Container with Port Mappings
## 🔹 Subtask 2.1: Build the Image
```
podman build -t exposed-app .
```
## 🔹 Subtask 2.2: Run with Port Mapping
Map container port 8080 to host port 8080:

```
podman run -d -p 8080:8080 --name webapp exposed-app
```
Check running containers:

```
podman ps
```
✅ Expected output: container running with 0.0.0.0:8080->8080/tcp.

⚠️ Troubleshooting:
- If port is already in use → try:

```
podman run -d -p 8081:8080 --name webapp exposed-app
```
---

# 📝 Task 3: Test Connectivity from Host
## 🔹 Subtask 3.1: Verify Port Mapping
Check if host port is bound:

```
ss -tulnp | grep 8080
```
Test the app:

```
curl http://localhost:8080
```
✅ Output:

csharp
```
Hello from the exposed container port!
```
## 🔹 Subtask 3.2: Test with Different Port Mapping
Stop previous container:

```
podman stop webapp
```
Run with different port:

```
podman run -d -p 9090:8080 --name webapp2 exposed-app
```
Test new mapping:

```
curl http://localhost:9090
```
---

# 📝 Task 4: Troubleshoot Port Conflicts
## 🔹 Subtask 4.1: Simulate Conflict
Try binding to already used port:

```
podman run -d -p 8080:8080 --name webapp3 exposed-app
```
✅ Expected: Error about port already in use

## 🔹 Subtask 4.2: Resolve Conflict
Find conflicting process:

```
sudo lsof -i :8080
```
Solutions:

- Stop the conflicting service

- Use a different host port (-p 9090:8080)

- Let Podman assign a random port:

```
podman run -d -P --name webapp4 exposed-app
```
```
podman port webapp4
```
---

# ✅ Conclusion
In this lab, you learned:

- How to document container ports using EXPOSE

- Difference between exposing and publishing ports

- Methods to map container → host ports

- How to troubleshoot port conflicts

---

## 🔍 Final Verification
List all containers:

```
podman ps -a
```
## Clean up:

```
podman stop -a && podman rm -a
```

---

📚 Additional Resources

- [Podman Documentation](https://docs.podman.io/en/latest/)

- [Dockerfile EXPOSE Reference](https://docs.docker.com/reference/dockerfile/#expose)

- Linux Networking Tools: man ss, man lsof
