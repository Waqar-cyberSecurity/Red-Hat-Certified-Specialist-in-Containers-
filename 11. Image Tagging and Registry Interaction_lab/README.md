# Lab 11: Image Tagging and Registry Interaction

## 🎯 Objectives
- Tag container images with semantic versioning
- Authenticate with container registries (Docker Hub or private)
- Push and pull images from registries
- Understand tag immutability and image digests

## 🛠 Prerequisites
- Podman (v3.0+)
- Docker Hub account (or private registry access)
- Linux CLI knowledge
- Internet connection

---

## 🚀 Lab Tasks

### Task 1: Tagging Images Semantically
**List existing images**
```bash
podman images
```
Tag image with semantic version

```
podman tag docker.io/library/nginx my-nginx:v1.0
```
Verify

```
podman images | grep nginx
# Shows both original and tagged image
```
✅ Use semantic versioning → vMAJOR.MINOR.PATCH

---

# Task 2: Logging into a Registry
Login to Docker Hub

```
podman login docker.io
# Enter username & password
```
Verify login

```
podman info | grep -A 5 "registries"
```
---

# Task 3: Pushing Images
Tag with registry prefix

```
podman tag my-nginx:v1.0 docker.io/<username>/my-nginx:v1.0
```
Push

```
podman push docker.io/<username>/my-nginx:v1.0
```
Verify → Check image on Docker Hub web interface.

---

# Task 4: Pulling Images
Remove local image

```
podman rmi docker.io/<username>/my-nginx:v1.0
```
Pull again

```
podman pull docker.io/<username>/my-nginx:v1.0
```
Verify

```
podman images
```
---

# Task 5: Tag Immutability & Digests
Check digest

```
podman inspect --format '{{.Digest}}' docker.io/<username>/my-nginx:v1.0
```
Pull by digest

```
podman pull docker.io/<username>/my-nginx@<digest>
```
Test tag immutability

- Push a different image with the same tag

- Note that digest changes, but tag name stays the same

 🔑 Concept:

- Tags → mutable labels

- Digests → immutable, unique identifiers

## 🧹 Cleanup
Remove all images:

```
podman rmi -f $(podman images -q)
```
⚠️ This deletes all local images.

---

## 📚 Next Steps
- Set up a private registry with Podman

- Learn image signing & verification

- Practice multi-architecture tagging
