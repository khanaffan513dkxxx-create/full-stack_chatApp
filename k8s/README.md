# 🚀 Full-Stack Chat Application — Kubernetes Deployment

A full-stack real-time chat application deployed locally using **Docker, Kubernetes, and Kind/Minikube**.

> **Project note:** The original application code was forked from the upstream repository. I independently created, customized, and tested the Kubernetes deployment manifests and deployment workflow for this project.

## 👨‍💻 My Contribution

- Created Kubernetes manifests inside the `k8s/` directory.
- Configured separate Deployments for the frontend, backend, and MongoDB.
- Created Kubernetes Services for internal application communication.
- Created a dedicated `chat-app` namespace for resource isolation.
- Configured MongoDB persistent storage using PV/PVC resources.
- Added backend configuration through Kubernetes environment variables and Secrets.
- Configured Nginx to serve the frontend and route API/Socket.IO traffic to the backend.
- Built and deployed the frontend and backend Docker images.
- Used Kubernetes commands for rollout, troubleshooting, logging, and port-forwarding.
- Debugged issues involving MongoDB authentication, service discovery, backend connectivity, and frontend access.

## 🏗️ Architecture

```text
Browser
   |
   v
Frontend Service (Nginx :80)
   |
   +---- /api/ and /socket.io/ ----> Backend Service (:5001)
                                      |
                                      v
                               MongoDB Service (:27017)
                                      |
                                      v
                               Persistent Volume
```

## 🛠️ Technologies Used

- **Frontend:** React / Nginx
- **Backend:** Node.js / Express
- **Database:** MongoDB
- **Containerization:** Docker
- **Orchestration:** Kubernetes
- **Local Cluster:** Kind or Minikube
- **Configuration:** Kubernetes YAML, ConfigMap, Secret
- **Storage:** PersistentVolume and PersistentVolumeClaim

## 📁 Kubernetes Manifests

| File | Purpose |
|---|---|
| `namespace.yaml` | Creates the `chat-app` namespace |
| `backend-deployment.yaml` | Deploys the backend application |
| `backend-service.yaml` | Exposes the backend inside the cluster |
| `backend-secrets.yaml` | Stores sensitive backend configuration |
| `frontend-deployment.yaml` | Deploys the frontend/Nginx container |
| `frontend-service.yaml` | Exposes the frontend application |
| `frontend-configmap.yaml` | Provides Nginx reverse-proxy configuration |
| `mongodb-deployment.yaml` | Deploys MongoDB |
| `mongodb-service.yaml` | Exposes MongoDB inside the cluster |
| `mongo-pvc.yaml` | Provides persistent storage for MongoDB |
| `kind-config.yaml` | Local Kind cluster configuration |

## 🚀 Deployment Steps

### 1. Clone the repository

```bash
git clone https://github.com/khanaffan513dkxxx-create/full-stack_chatApp.git
cd full-stack_chatApp
```

### 2. Create or start the local Kubernetes cluster

For Kind:

```bash
kind create cluster --config k8s/kind-config.yaml
kubectl cluster-info
```

For Minikube, start Minikube using your preferred driver and verify the cluster:

```bash
minikube status
kubectl cluster-info
```

### 3. Create the namespace

```bash
kubectl apply -f k8s/namespace.yaml
```

### 4. Deploy storage, configuration, and Secrets

```bash
kubectl apply -f k8s/mongo-pvc.yaml -n chat-app
kubectl apply -f k8s/backend-secrets.yaml -n chat-app
kubectl apply -f k8s/frontend-configmap.yaml -n chat-app
```

### 5. Deploy MongoDB

```bash
kubectl apply -f k8s/mongodb-deployment.yaml -n chat-app
kubectl apply -f k8s/mongodb-service.yaml -n chat-app
kubectl wait --for=condition=Ready pod -l app=mongodb -n chat-app --timeout=120s
```

### 6. Deploy the backend

```bash
kubectl apply -f k8s/backend-deployment.yaml -n chat-app
kubectl apply -f k8s/backend-service.yaml -n chat-app
kubectl rollout status deployment/backend -n chat-app
```

### 7. Deploy the frontend

```bash
kubectl apply -f k8s/frontend-deployment.yaml -n chat-app
kubectl apply -f k8s/frontend-service.yaml -n chat-app
kubectl rollout status deployment/frontend -n chat-app
```

## 🔍 Verification and Troubleshooting

Check all resources:

```bash
kubectl get all -n chat-app
```

Check services:

```bash
kubectl get svc -n chat-app
```

View application logs:

```bash
kubectl logs -l app=backend -n chat-app
kubectl logs -l app=frontend -n chat-app
kubectl logs -l app=mongodb -n chat-app
```

Describe a resource when troubleshooting:

```bash
kubectl describe pod <pod-name> -n chat-app
```

Check backend service endpoints:

```bash
kubectl get endpoints backend -n chat-app
```

## 🌐 Access the Application

Use port-forwarding when running the application locally:

```bash
kubectl port-forward svc/frontend -n chat-app 8080:80 --address=0.0.0.0
```

Then open:

```text
http://localhost:8080
```

Keep the port-forward command running while using the application. Press `Ctrl+C` to stop it.

## 🐳 Docker Compose Alternative

The application can also be run using Docker Compose:

```bash
docker compose up -d --build
```

Stop the Compose services:

```bash
docker compose down
```

## 📌 Learning Outcomes

This project helped me practice:

- Containerizing full-stack applications with Docker.
- Deploying multi-tier applications on Kubernetes.
- Kubernetes Deployments, Services, Namespaces, Secrets, ConfigMaps, and storage.
- Internal service-to-service communication using Kubernetes DNS.
- Nginx reverse proxy configuration.
- MongoDB persistence and authentication configuration.
- Debugging CrashLoopBackOff, service connectivity, and port-forwarding issues.

## 📄 Project Attribution

The application source is based on a fork of the upstream full-stack chat application repository. The Kubernetes manifests, deployment configuration, testing, and troubleshooting work documented here were completed and customized as part of my DevOps learning project.
