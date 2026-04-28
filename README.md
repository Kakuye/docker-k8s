# Docker & Kubernetes Hands-On Lab Guide (Beginner Friendly)

This guide is **100% lab-based** and designed for beginners. Every concept is paired with commands you can run immediately.

# PART 1: DOCKER LABS

## Lab 1: Install Docker (Ubuntu)

### Step 1: Update system

```bash
sudo apt update
sudo apt upgrade -y
```

### Step 2: Install dependencies

```bash
sudo apt install -y ca-certificates curl gnupg
```

### Step 3: Add Docker GPG key

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

### Step 4: Add repository

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Step 5: Install Docker

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

### Step 6: Verify

```bash
docker --version
sudo docker run hello-world
```

### Explanation

* Docker is a **container engine** that runs applications in isolated environments.
* `hello-world` confirms Docker works correctly.

---

## Lab 2: Basic Docker Commands

### Run container

```bash
docker run -it ubuntu bash
```

### List containers

```bash
docker ps
docker ps -a
```

### Stop container

```bash
docker stop <container_id>
```

### Remove container

```bash
docker rm <container_id>
```

### Explanation

* `-it` gives interactive terminal
* Containers are **temporary environments**

---

## Lab 3: Working with Images

### Pull image

```bash
docker pull nginx
```

### List images

```bash
docker images
```

### Remove image

```bash
docker rmi nginx
```

### Explanation

* Images are **templates** for containers
* Containers are **running instances** of images

---

## Lab 4: Run Web Server (Nginx)

```bash
docker run -d -p 8080:80 nginx
```

### Test

Open browser:

```
http://localhost:8080
```

### Explanation

* `-d` runs in background
* `-p` maps ports (host:container)

---

## Lab 5: Create Your Own Image (Dockerfile)

### Step 1: Create project

```bash
mkdir myapp
cd myapp
```

### Step 2: Create Dockerfile

```bash
nano Dockerfile
```

Paste:

```Dockerfile
FROM ubuntu
RUN apt update && apt install -y nginx
CMD ["nginx", "-g", "daemon off;"]
```

### Step 3: Build image

```bash
docker build -t mynginx .
```

### Step 4: Run

```bash
docker run -d -p 8081:80 mynginx
```

### Explanation

* Dockerfile defines how image is built
* `-t` tags image

---

## Lab 6: Volumes (Persistent Storage)

```bash
docker run -d -p 8082:80 -v mydata:/usr/share/nginx/html nginx
```

### Explanation

* Volumes store data **outside containers**
* Prevent data loss when container stops

---

---

## Advanced Docker Commands, Options & Real Use Cases

This section expands your practical Docker knowledge with **real-world usage patterns**.

---

### 1. Container Lifecycle Deep Dive

#### Create vs Run

```bash
docker create nginx
docker start <container_id>
```

* `create` = prepares container
* `start` = runs it

#### Restart Policies

```bash
docker run -d --restart=always nginx
```

Options:

* `no` (default)
* `on-failure`
* `always`
* `unless-stopped`

**Use Case:** Production services that must auto-recover.

---

### 2. Interactive vs Detached Mode

#### Interactive

```bash
docker run -it ubuntu bash
```

#### Detached

```bash
docker run -d nginx
```

#### Attach to running container

```bash
docker attach <container_id>
```

**Use Case:**

* `-it` → debugging
* `-d` → background services

---

### 3. Port Mapping Advanced

```bash
docker run -d -p 8080:80 nginx
```

Multiple ports:

```bash
docker run -d -p 8080:80 -p 8443:443 nginx
```

Bind to specific IP:

```bash
docker run -d -p 127.0.0.1:8080:80 nginx
```

**Use Case:** Secure local-only services.

---

### 4. Environment Variables

```bash
docker run -d -e MYSQL_ROOT_PASSWORD=secret mysql
```

From file:

```bash
docker run --env-file .env nginx
```

**Use Case:** Config management without hardcoding.

---

### 5. Resource Limits (VERY IMPORTANT)

```bash
docker run -d --memory="512m" --cpus="1.0" nginx
```

**Use Case:** Prevent one container from crashing entire server.

---

### 6. Logs & Monitoring

```bash
docker logs <container_id>
docker logs -f <container_id>
```

Limit logs:

```bash
docker run -d --log-opt max-size=10m nginx
```

**Use Case:** Troubleshooting production issues.

---

### 7. Exec vs Attach (Important Difference)

```bash
docker exec -it <container_id> bash
```

* `exec` = new shell
* `attach` = existing process

**Best Practice:** Always use `exec`

---

### 8. Networking

#### List networks

```bash
docker network ls
```

#### Create network

```bash
docker network create mynet
```

#### Run container in network

```bash
docker run -d --network mynet --name web nginx
```

#### Test communication

```bash
docker run -it --network mynet busybox ping web
```

**Use Case:** Microservices communication.

---

### 9. Volumes vs Bind Mounts

#### Volume

```bash
docker volume create mydata
```

```bash
docker run -v mydata:/data nginx
```

#### Bind Mount

```bash
docker run -v $(pwd):/app nginx
```

**Difference:**

* Volume → managed by Docker
* Bind → local filesystem

**Use Case:**

* Volume → production
* Bind → development

---

### 10. Inspect & Metadata

```bash
docker inspect <container_id>
```

Filter output:

```bash
docker inspect -f '{{.State.Status}}' <container_id>
```

**Use Case:** Debugging networking, IP, mounts.

---

### 11. Image Management Advanced

#### Tagging

```bash
docker tag nginx myrepo/nginx:v1
```

#### Save image

```bash
docker save -o nginx.tar nginx
```

#### Load image

```bash
docker load -i nginx.tar
```

**Use Case:** Offline environments.

---

### 12. Cleanup (VERY IMPORTANT)

#### Remove stopped containers

```bash
docker container prune
```

#### Remove unused images

```bash
docker image prune
```

#### Full cleanup

```bash
docker system prune -a
```

**Warning:** Deletes unused resources.

---

### 13. Multi-Container with Docker Compose

#### Install

```bash
sudo apt install docker-compose -y
```

#### Example docker-compose.yml

```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  db:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
```

#### Run

```bash
docker-compose up -d
```

#### Stop

```bash
docker-compose down
```

**Use Case:** Full application stack (web + DB).

---

### 14. Real-World Use Case Labs

#### Lab A: Run Web + Database

```bash
docker network create appnet

docker run -d --name db --network appnet -e MYSQL_ROOT_PASSWORD=secret mysql

docker run -d --name web --network appnet -p 8080:80 nginx
```

---

#### Lab B: Debug Failing Container

```bash
docker logs <container_id>
docker exec -it <container_id> bash
```

---

#### Lab C: Limit Resources

```bash
docker run -d --memory=256m nginx
```

---

#### Lab D: Persistent Website Data

```bash
mkdir html
echo "Hello Docker" > html/index.html

docker run -d -p 8085:80 -v $(pwd)/html:/usr/share/nginx/html nginx
```

---

### Key Takeaways

* Docker is not just run/stop → it's about **control & isolation**
* Always use:

  * resource limits
  * proper networking
  * volumes for data

---



# PART 2: KUBERNETES LABS

## Lab 7: Install Minikube (Local Kubernetes)

### Install kubectl

```bash
sudo apt install -y kubectl
```

### Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### Start cluster

```bash
minikube start
```

### Verify

```bash
kubectl get nodes
```

### Explanation

* Minikube runs Kubernetes locally
* kubectl is CLI to manage cluster

---

## Lab 8: First Pod

```bash
kubectl run nginx --image=nginx
```

### Check

```bash
kubectl get pods
```

### Describe

```bash
kubectl describe pod nginx
```

### Explanation

* Pod = smallest deployable unit in Kubernetes

---

## Lab 9: Expose Pod

```bash
kubectl expose pod nginx --type=NodePort --port=80
```

### Get URL

```bash
minikube service nginx
```

### Explanation

* Service exposes pod to network

---

## Lab 10: Deployments (Scaling)

```bash
kubectl create deployment myapp --image=nginx
```

### Scale

```bash
kubectl scale deployment myapp --replicas=3
```

### Check

```bash
kubectl get pods
```

### Explanation

* Deployment manages multiple pods
* Ensures high availability

---

## Lab 11: YAML Deployment

### Create file

```bash
nano deployment.yaml
```

Paste:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

### Apply

```bash
kubectl apply -f deployment.yaml
```

### Explanation

* YAML defines desired state
* Kubernetes ensures it is maintained

---

## Lab 12: Service YAML

```bash
nano service.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
```

### Apply

```bash
kubectl apply -f service.yaml
```

---

## Lab 13: Debugging

### Logs

```bash
kubectl logs <pod-name>
```

### Exec into pod

```bash
kubectl exec -it <pod-name> -- bash
```

### Delete

```bash
kubectl delete pod <pod-name>
```

### Explanation

* Logs help troubleshooting
* Exec allows inside container access

---

# FINAL LAB: FULL APPLICATION DEPLOYMENT

1. Build Docker image
2. Push to Docker Hub
3. Deploy in Kubernetes

### Example

```bash
docker tag mynginx username/mynginx
docker push username/mynginx
```

Update deployment YAML with your image.

---

# KEY CONCEPT SUMMARY

| Concept    | Meaning             |
| ---------- | ------------------- |
| Image      | Blueprint           |
| Container  | Running app         |
| Pod        | Group of containers |
| Deployment | Manages pods        |
| Service    | Networking          |

---
