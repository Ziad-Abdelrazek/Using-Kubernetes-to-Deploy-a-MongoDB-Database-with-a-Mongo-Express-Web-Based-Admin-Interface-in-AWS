# MongoDB & Mongo Express Deployment on Kubernetes (AWS)

## Project Overview

This project demonstrates a complete DevOps deployment of a MongoDB database and a Mongo Express web-based administration interface on Kubernetes running on Amazon Web Services (AWS).

The deployment focuses on production-like concepts such as persistent storage, secrets management, configuration management, and service exposure using native Kubernetes resources.

### Project Objectives

- Kubernetes storage using AWS EBS CSI Driver  
- Secure credential handling using Kubernetes Secrets  
- Configuration decoupling using ConfigMaps  
- Stateful application deployment (MongoDB)  
- Application-to-application communication inside the cluster  

---

## Architecture Overview

The architecture consists of multiple components running inside a Kubernetes cluster hosted on AWS.

### MongoDB
- Deployed as a Kubernetes Deployment with a single replica  
- Uses persistent storage backed by AWS EBS  
- Credentials injected securely using Kubernetes Secrets  

### Mongo Express
- Web-based MongoDB administration interface  
- Connects to MongoDB using an internal Kubernetes Service  
- Uses Secrets and ConfigMaps for configuration  

### Storage
- AWS EBS volumes provisioned dynamically via a StorageClass  
- PersistentVolumeClaim ensures MongoDB data durability  

### Networking
- Internal service-to-service communication inside the cluster  
- NodePort services expose applications externally for testing  

---

## High-Level Architecture Flow

User  
→ NodePort Service (mongo-express-svc)  
→ Mongo Express Pod  
→ MongoDB Service (mongodb-svc)  
→ MongoDB Pod  
→ AWS EBS Volume (Persistent Storage)  

---

## Kubernetes Components Used

### 1. Secrets
Used to store MongoDB credentials securely.

- Username and password are base64 encoded  
- Injected as environment variables into both MongoDB and Mongo Express  

**File:**  

---

### 2. StorageClass
Defines dynamic provisioning of storage using AWS EBS CSI driver.

- Provisioner: `ebs.csi.aws.com`  
- Volume binding mode: `WaitForFirstConsumer`  

**File:**  

---

### 3. PersistentVolumeClaim
Requests storage from the StorageClass for MongoDB data persistence.

- Access mode: ReadWriteOnce  
- Storage size: 5Gi  

**File:**  

---

### 4. MongoDB Deployment
Deploys the MongoDB container with persistent storage.

**Key points:**
- Image: `mongo:4.4`  
- Environment variables loaded from Kubernetes Secret  
- Data stored in `/data/db`  

**File:**  

---

### 5. MongoDB Service
Exposes MongoDB internally inside the cluster.

- Service type: NodePort  
- Port: 27017  

**File:**  

---

### 6. ConfigMap
Stores non-sensitive configuration data for Mongo Express.

- Holds MongoDB service name as the database endpoint  

**File:**  

---

### 7. Mongo Express Deployment
Deploys the Mongo Express web UI.

**Key points:**
- Image: `mongo-express:0.54`  
- Connects to MongoDB via Kubernetes Service  
- Uses Secret and ConfigMap for configuration  

**File:**  

---

### 8. Mongo Express Service
Exposes Mongo Express UI externally.

- Service type: NodePort  
- Port: 8081  

**File:**  

---

## Project Structure


mongo-db-project/
├── mongodb-app.yml
├── mongodb-cm.yml
├── mongodb-pvc.yml
├── mongodb-sc.yml
├── mongodb-svc.yml
├── mongoexpress-app.yml
├── mongoexpress-svc.yml
└── secret.yml



---

## Deployment Steps

Apply the Kubernetes manifests in the following order:

```bash
kubectl apply -f secret.yml
kubectl apply -f mongodb-sc.yml
kubectl apply -f mongodb-pvc.yml
kubectl apply -f mongodb-app.yml
kubectl apply -f mongodb-svc.yml
kubectl apply -f mongodb-cm.yml
kubectl apply -f mongoexpress-app.yml
kubectl apply -f mongoexpress-svc.yml
Accessing Mongo Express

Get the NodePort service:

kubectl get svc mongo-express-svc
Access the application from your browser:

http://13.220.189.30:32412/
Key DevOps Concepts Demonstrated

Kubernetes Secrets for sensitive data

ConfigMaps for application configuration

Persistent storage using AWS EBS

Service discovery using Kubernetes Services

Separation of configuration from application code

Cloud-native deployment on AWS Kubernetes infrastructure

Future Improvements

Use StatefulSet for MongoDB instead of Deployment

Add resource limits and requests

Add liveness and readiness probes

Secure access using Ingress with TLS

Integrate CI/CD pipeline using GitHub Actions

Migrate NodePort services to LoadBalancer or Ingress

Technologies Used

Kubernetes

Amazon Web Services (AWS)

Docker

MongoDB

Mongo Express

