@"
# EasyCRUD - 3 Tier Student Registration App on Kubernetes

## 🚀 Project Overview
EasyCRUD is a full-stack Student Registration application.
This project demonstrates how to deploy a 3-tier application 
on Kubernetes using Docker containers, covering real-world 
DevOps concepts like StatefulSets, PVC, HPA, Ingress, 
ConfigMaps and Secrets.

## 🏗️ Application Architecture
User Browser
added one line

↓

Ingress Controller (Traffic Routing)

↓           ↓

/             /api

↓              ↓

Frontend      Backend

Service       Service

(NodePort)    (ClusterIP)

↓              ↓

Frontend      Backend Pod

Pod           (Spring Boot)

(React+Nginx)      ↓

MariaDB Service

(ClusterIP)

↓

MariaDB Pod

(StatefulSet)

↓

PersistentVolume

(Data Storage)

## 🛠️ Tech Stack
| Layer | Technology |
|-------|-----------|
| Frontend | React + Vite + Nginx |
| Backend | Spring Boot (Java 21) |
| Database | MariaDB |
| Container | Docker |
| Orchestration | Kubernetes |
| Local Cluster | Minikube |
| Cloud Cluster | AWS EKS |

---

## ☸️ Kubernetes Components

### 🗄️ Database (MariaDB)
| Component | Purpose |
|-----------|---------|
| StatefulSet | Runs MariaDB pod with unique identity |
| PVC (1Gi) | Persists data even if pod restarts |
| ConfigMap | Stores DB name, host, port |
| Secret | Stores username and password (base64) |
| Service (ClusterIP) | Internal access only on port 3306 |

### ⚙️ Backend (Spring Boot)
| Component | Purpose |
|-----------|---------|
| Deployment | Runs 2 replicas for high availability |
| Service (ClusterIP) | Internal access on port 8080 |
| HPA | Auto scales 2-5 pods when CPU > 70% |
| ConfigMap | DB connection details as env variables |
| Secret | DB credentials as env variables |

### 🖥️ Frontend (React + Nginx)
| Component | Purpose |
|-----------|---------|
| Deployment | Runs 2 replicas for high availability |
| Service (NodePort) | External user access on port 80 |
| HPA | Auto scales 2-5 pods when CPU > 70% |
| Ingress | Routes / to frontend, /api to backend |

---

## 🐳 Docker Images

### Multi-Stage Build — Backend
``````dockerfile
# Stage 1 - Build JAR
FROM maven:3.9.8-eclipse-temurin-21 AS build
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

# Stage 2 - Run JAR
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
``````

### Multi-Stage Build — Frontend
``````dockerfile
# Stage 1 - Build React
FROM node:18-alpine AS build
WORKDIR /app
COPY package*.json .
RUN npm install
COPY . .
RUN npm run build

# Stage 2 - Serve with Nginx
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
``````

### DockerHub Images
- ``shubhamthakur143/backend-image:v1``
- ``shubhamthakur143/frontend-image:v3``

---

## 📦 How to Deploy

### Prerequisites
- Docker Desktop installed and running
- Minikube installed
- kubectl installed

### Step 1 - Start Minikube
``````bash
minikube start --driver=docker --cpus=4 --memory=7000
minikube addons enable ingress
minikube addons enable metrics-server
``````

### Step 2 - Deploy Database
``````bash
kubectl apply -f database/
``````

### Step 3 - Deploy Backend
``````bash
kubectl apply -f backend/
``````

### Step 4 - Deploy Frontend
``````bash
kubectl apply -f frontend/
``````

### Step 5 - Access Application
``````bash
minikube service frontend-service --url
``````
Open the URL in browser!

---

## 🔍 Verify Deployment
``````bash
# Check all resources
kubectl get all

# Check storage
kubectl get pvc

# Check auto scaling
kubectl get hpa

# Check ingress
kubectl get ingress

# Check logs
kubectl logs deployment/backend-deployment
``````

---

## 📁 Project Structure
EasyCRUD/

├── backend/

│   ├── src/                    # Spring Boot source code

│   ├── Dockerfile              # Multi-stage Docker build

│   ├── deployment.yml          # K8s Deployment - 2 replicas

│   ├── service.yml             # K8s Service - ClusterIP

│   └── hpa.yml                 # HPA - scales 2 to 5 pods

├── frontend/

│   ├── src/                    # React source code

│   ├── Dockerfile              # Multi-stage Docker build

│   ├── deployment.yml          # K8s Deployment - 2 replicas

│   ├── service.yml             # K8s Service - NodePort

│   ├── hpa.yml                 # HPA - scales 2 to 5 pods

│   └── ingress.yml             # Ingress - traffic routing

├── database/

│   ├── configmap.yml           # DB name, host, port

│   ├── secret.yml              # DB credentials (base64)

│   ├── pvc.yml                 # 1Gi persistent storage

│   ├── statefulset.yml         # MariaDB StatefulSet

│   └── service.yml             # ClusterIP - port 3306

└── README.md

---

## 🎯 Key Concepts Learned
- **StatefulSet vs Deployment** - Database needs stable identity
- **PV and PVC** - Data persistence beyond pod lifecycle
- **ConfigMap and Secrets** - Secure configuration management
- **HPA** - Automatic pod scaling based on CPU usage
- **Ingress** - Single entry point for traffic routing
- **Multi-stage Docker** - Smaller and optimized images
- **AWS EKS** - Managed Kubernetes on cloud
- **Service Types** - ClusterIP vs NodePort vs LoadBalancer

---

## 👨‍💻 Author
**Shubham Thakur**
- GitHub: [shubhamthakur143](https://github.com/shubhamthakur143)
- LinkedIn: [Shubham Thakur](https://www.linkedin.com/in/shubham-thakur-a73476368)
"@ | Out-File -FilePath C:\WINDOWS\system32\EasyCRUD\README.md -Encoding utf8
``````
