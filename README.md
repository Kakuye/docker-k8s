# docker-k8s

# Docker & Kubernetes Hands-On Lab Guide (Beginner Friendly)

This guide is **100% lab-based** and designed for beginners. Every concept is paired with commands you can run immediately.

---

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

# NEXT STEPS

* Learn Helm
* Learn Ingress
* Learn CI/CD pipelines

---

This lab is designed to be repeated multiple times until comfortable.
