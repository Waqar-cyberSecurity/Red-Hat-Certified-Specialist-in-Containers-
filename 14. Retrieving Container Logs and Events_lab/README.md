# Lab 14: Retrieving Container Logs and Events

## 🎯 Objectives
By the end of this lab, you will be able to:
- View and filter container logs using **Podman**  
- Monitor **container lifecycle events**  
- Configure **alternative log drivers**  
- Analyze logs and events for troubleshooting purposes  

---

## 🛠 Prerequisites
- Podman installed (version 3.0+ recommended)  
- Basic familiarity with Linux CLI  
- A running container (we’ll create one during the lab)  

---

## ⚙️ Lab Setup
Verify installation:
```bash
podman --version
```

Pull sample image:

```
podman pull docker.io/library/nginx:alpine
```
---

## 🚀 Tasks
# Task 1: Viewing Container Logs
## Subtask 1.1: Basic Log Viewing
Run a container in detached mode:

```
podman run -d --name nginx-container docker.io/library/nginx:alpine
```
View logs:

```
podman logs nginx-container
```
✅ Expected: NGINX startup logs

## Subtask 1.2: Following Logs in Real-Time
```
podman logs --follow nginx-container
```
➡️ Press Ctrl+C to stop

## Subtask 1.3: Filtering Logs by Time
Last 5 minutes:

```
podman logs --since 5m nginx-container
```
Between specific times:

```
podman logs --since 2023-01-01T00:00:00 --until 2023-01-01T12:00:00 nginx-container
```
---

# Task 2: Configuring Alternative Log Drivers
## Subtask 2.1: JSON File Logging
```
podman run -d --name json-logger --log-driver json-file docker.io/library/nginx:alpine
```
Check log file path:

```
podman inspect --format '{{.HostConfig.LogConfig.Path}}' json-logger
```
## Subtask 2.2: Journald Logging
```
podman run -d --name journald-logger --log-driver journald docker.io/library/nginx:alpine
```
View logs in journald:

```
journalctl CONTAINER_NAME=journald-logger
```
---

# Task 3: Monitoring Container Events
## Subtask 3.1: Basic Event Monitoring
Terminal 1:

```
podman events --format "{{.Time}} {{.Type}} {{.Status}}"
```
Terminal 2:

```
podman run -d --name event-test docker.io/library/nginx:alpine
```
✅ Expected: Container creation & start events

## Subtask 3.2: Filtering Events
Only start events:

```
podman events --filter event=start
```
Specific container:

```
podman events --filter container=event-test
```
Event type + time:

```
podman events --filter event=die --since 1h
```
---

# Task 4: Troubleshooting with Logs and Events
## Subtask 4.1: Analyzing a Failing Container
Run failing container:

```
podman run --name failing-container docker.io/library/alpine /bin/nonexistent-command
```
Check logs:

```
podman logs failing-container
```
Check events:

```
podman events --filter container=failing-container --since 1m
```
Subtask 4.2: Debugging with Detailed Logs
Run with debug logging:

```
podman run -d --name debug-container --log-level=debug docker.io/library/nginx:alpine
```
View debug logs:

```
podman logs debug-container
```
---

# ✅ Conclusion
In this lab, you learned:

- Viewing and following container logs with Podman

- Filtering logs by time and range

- Using JSON-file and Journald log drivers

- Monitoring container lifecycle events

- Troubleshooting failing containers with logs and events

These are essential troubleshooting skills for production environments.

## 🧹 Cleanup
Remove all containers created in this lab:

```
podman rm -f nginx-container json-logger journald-logger event-test failing-container debug-container
```
---
# 📚 Additional Resources

- [Podman Documentation](https://docs.podman.io/en/latest/)
- [Journald Documentation](https://www.freedesktop.org/software/systemd/man/latest/journalctl.html)
