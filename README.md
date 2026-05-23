# Cloud-Native KYC Platform

A production-style cloud-native KYC document management platform built using **Go (Golang)**, **self-managed Kubernetes (kubeadm)**, **AWS**, **PostgreSQL**, **Amazon S3**, and **AWS Application Load Balancer (ALB)**.

This project demonstrates end-to-end deployment of a scalable backend application on a self-managed Kubernetes cluster hosted on AWS, following cloud-native architecture principles such as container orchestration, infrastructure isolation, load balancing, application resilience, health monitoring, and persistent cloud storage integration.

The platform allows users to submit personal information along with KYC documents through a web interface. Uploaded documents are stored in **Amazon S3**, while user metadata is persisted in **Amazon RDS PostgreSQL**. The application is deployed as replicated Kubernetes workloads behind an internet-facing **AWS ALB**, providing resilient and production-style traffic handling.

---

# Business Use Case

Many enterprise applications require structured customer onboarding workflows involving document submission, metadata persistence, scalable backend processing, and durable storage.

This project simulates a simplified **Know Your Customer (KYC)** onboarding workflow where:

- Users submit personal information
- Users upload KYC documents
- Documents are stored in durable cloud object storage
- User metadata is stored in a relational database
- Application traffic is distributed across multiple replicas
- Application remains resilient if individual pods fail

This architecture reflects common real-world patterns used in:

- FinTech platforms
- Banking KYC workflows
- Insurance onboarding systems
- Telecom customer verification
- SaaS document submission platforms

---

# Key Features

## Application Features

- User registration form
- Multipart document upload support
- Amazon S3 document storage integration
- PostgreSQL metadata persistence
- Structured application logging
- Health check endpoint
- Readiness check endpoint
- Replica-aware response handling

---

## Kubernetes Features

- Self-managed Kubernetes cluster using kubeadm
- Multi-node cluster deployment
- Containerized Go application deployment
- Replica-based workload scaling
- Kubernetes Deployment-based lifecycle management
- NodePort service exposure
- Kubernetes Secret-based runtime configuration
- Liveness probes
- Readiness probes
- Pod self-healing
- Rolling update support

---

## AWS Infrastructure Features

- Custom VPC networking
- Public and private subnet isolation
- NAT Gateway outbound access design
- AWS Application Load Balancer (ALB)
- Amazon RDS PostgreSQL
- Amazon S3 object storage
- AWS Load Balancer Controller integration
- IAM-authenticated AWS API access
- Security Group-based traffic control

---

# Technology Stack

## Backend

- Go (Golang 1.23)
- net/http
- database/sql
- PostgreSQL driver
- AWS SDK for Go v2

---

## Cloud Platform

**Amazon Web Services (AWS)**

Services used:

- EC2
- VPC
- Application Load Balancer
- RDS PostgreSQL
- S3
- IAM
- NAT Gateway
- Security Groups

---

## Containerization

- Docker
- containerd

---

## Kubernetes

- kubeadm
- kubelet
- kubectl
- Calico CNI
- AWS Load Balancer Controller

---

## Database

- PostgreSQL

---

## Deployment

- DockerHub image registry
- Kubernetes manifests

---

## Operating System

- Ubuntu Linux

---

# High-Level Architecture

<img width="1536" height="1024" alt="Cloud Native KYC Platform Architecture" src="https://github.com/user-attachments/assets/eef00572-30c3-44d6-bde3-6ce551c7e172" />

---

# Architecture Overview

This platform is deployed on a **self-managed Kubernetes cluster hosted on AWS infrastructure**.

### Architecture Flow

```text
Internet Users
    ↓
AWS Application Load Balancer (Public)
    ↓
Kubernetes NodePort Service
    ↓
Go Application Pods
    ↓
Amazon RDS PostgreSQL
    ↓
Amazon S3
```

---

## Kubernetes Control Plane

A dedicated control plane node manages the Kubernetes cluster.

Responsibilities:

- Kubernetes API server management
- Cluster scheduling
- Desired state management
- Controller reconciliation
- Cluster orchestration

---

## Worker Nodes

Two worker nodes host the application workloads.

Responsibilities:

- Running Go application pods
- Processing incoming traffic
- Communicating with backend AWS services
- Executing container workloads

Application workloads are distributed across worker nodes for resilience.

---

## AWS Application Load Balancer (ALB)

A public-facing ALB exposes the application to internet users.

Responsibilities:

- Accept incoming HTTP traffic
- Perform health checks
- Route requests to Kubernetes worker nodes
- Provide external application access

Traffic flow:

```text
ALB → Worker Nodes → NodePort Service → Pods
```

---

## Kubernetes Service Layer

A NodePort service provides stable service routing.

Responsibilities:

- Route traffic to healthy pods
- Abstract dynamic pod IP changes
- Enable ALB integration
- Provide service-level load balancing

---

## Application Layer

The Go application provides:

- Web UI
- HTTP request handling
- Multipart file processing
- PostgreSQL integration
- Amazon S3 integration
- Structured logging
- Health monitoring endpoints

The application runs as multiple replicas for workload resilience.

---

## Database Layer

Amazon RDS PostgreSQL stores application metadata.

Stored data includes:

- User name
- Email
- Phone number
- S3 bucket name
- Document object key
- KYC workflow status
- Timestamps

---

## Object Storage Layer

Amazon S3 stores uploaded KYC documents.

Benefits:

- Durable cloud storage
- Elastic scalability
- Separation of compute and storage
- Simplified document management

---

## Network Security Layer

Infrastructure isolation is implemented using AWS networking controls.

Components:

- Custom VPC
- Public subnets
- Private subnets
- NAT Gateway
- Security Groups

This protects backend resources from direct internet exposure.

---

# Why This Project Matters

This project demonstrates practical experience in:

✅ Cloud-native application architecture  
✅ Kubernetes orchestration  
✅ Self-managed Kubernetes administration  
✅ AWS infrastructure design  
✅ Backend development with Go  
✅ Cloud storage integration  
✅ Database integration  
✅ Application resilience  
✅ Production-style deployment patterns  
✅ Infrastructure isolation  
✅ Health monitoring implementation

---

# Project Structure

```text
aws_kubernetes_kubeadm_go_app/
├── go.mod
├── go.sum
├── index.html
├── main.go
├── Dockerfile
├── deployment.yaml
├── service.yaml
├── ingress.yaml
└── README.md
```

### Component Breakdown

- **go.mod** → Go module definition and dependency management
- **go.sum** → dependency checksum verification
- **index.html** → frontend user registration and KYC upload form
- **main.go** → core backend application logic
- **Dockerfile** → application container build instructions
- **deployment.yaml** → Kubernetes Deployment configuration
- **service.yaml** → Kubernetes NodePort service configuration
- **ingress.yaml** → AWS ALB ingress configuration
- **README.md** → project documentation

---

# Application Internals

The backend application is implemented in **Go** and follows a simple cloud-native service architecture.

Core responsibilities:

- Serve frontend UI
- Accept user input
- Process multipart file uploads
- Upload documents to Amazon S3
- Store metadata in PostgreSQL
- Expose health endpoints
- Provide replica-aware responses
- Integrate with Kubernetes lifecycle management

---

## Database Schema

The application automatically creates the required PostgreSQL table during startup.

```sql
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT NOT NULL,
    phone TEXT NOT NULL,
    document_bucket TEXT NOT NULL,
    document_key TEXT NOT NULL,
    kyc_status TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Stored information:

- User identity details
- Uploaded document storage location
- KYC workflow status
- Creation timestamp

---

## HTTP Endpoints

### Frontend UI

```http
GET /
```

Purpose:

- Serves HTML user form
- Accepts user registration input
- Provides KYC upload interface

---

### Form Submission

```http
POST /submit
```

Purpose:

- Receives multipart form data
- Extracts user details
- Processes uploaded document
- Uploads document to Amazon S3
- Stores metadata in PostgreSQL

---

### Health Check

```http
GET /health
```

Purpose:

- Application health verification
- Database connectivity validation
- Kubernetes liveness probe target
- ALB health check endpoint

Response:

```text
OK
```

---

### Readiness Check

```http
GET /ready
```

Purpose:

- Application readiness verification
- Database availability validation
- Kubernetes readiness probe target

---

## End-to-End Request Flow

### Step 1 — User Access

The user accesses the application through the ALB endpoint.

```text
User → AWS ALB → Kubernetes NodePort Service → Go Pod
```

---

### Step 2 — Form Rendering

The application serves:

```text
index.html
```

User interface includes:

- Name field
- Email field
- Phone field
- KYC document upload field

---

### Step 3 — Form Submission

The user submits:

- Personal information
- KYC document

The application receives:

```http
POST /submit
```

---

### Step 4 — S3 Upload

The uploaded document is stored in Amazon S3.

Generated object key format:

```text
kyc-docs/YYYYMMDD-HHMMSS-filename
```

Example:

```text
kyc-docs/20260523-103015-passport.pdf
```

This ensures unique object naming.

---

### Step 5 — Database Persistence

The application stores metadata in PostgreSQL.

Stored values:

- Name
- Email
- Phone
- S3 bucket name
- S3 object key
- KYC status

---

### Step 6 — Response

The application returns the serving pod identity.

Example:

```text
User data stored by instance: go-app-6d9c8f5d8c-hj92q
```

This helps verify traffic distribution across replicas.

---

## Runtime Configuration

Required environment variables:

```text
RDS_DB_HOST
RDS_DB_PORT
RDS_DB_USER
RDS_DB_PASSWORD
RDS_DB_NAME
RDS_DB_SSLMODE
S3_BUCKET_NAME
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

**Note:** AWS region is currently hardcoded in the application as:

```go
ap-south-1
```

Sensitive values are injected using Kubernetes Secrets.

---

## Logging

The application uses structured logging for operational visibility.

Logged events include:

- Application startup
- Database connection success
- Table initialization
- Invalid request methods
- S3 upload failures
- Database insert failures
- Successful user creation
- HTTP server startup

Example:

```text
level=INFO service=go-app event=user_created name=Mandar email=test@example.com
```

---

# Kubernetes Deployment Architecture

This project uses a **self-managed Kubernetes cluster (kubeadm)** for application orchestration.

Kubernetes responsibilities:

- Deployment management
- Replica lifecycle management
- Traffic routing
- Health monitoring
- Self-healing
- Rolling updates
- Service discovery

---

## Cluster Topology

```text
1 Control Plane Node
2 Worker Nodes
```

---

## Deployment

The Go application is deployed using a Kubernetes **Deployment** resource.

Deployment provides:

- Replica management
- Automatic failed pod replacement
- Rolling update support
- Desired state enforcement

Example:

```text
go-app
├── Pod 1 → Worker Node 1
└── Pod 2 → Worker Node 2
```

---

## Service

The application is exposed internally using a **NodePort Service**.

Purpose:

- Stable service access
- Traffic routing to healthy pods
- Abstraction from changing pod IPs
- ALB integration

Traffic flow:

```text
AWS ALB → Worker Node → NodePort Service → Go Pods
```

---

## Ingress

External traffic is routed through Kubernetes Ingress integrated with AWS ALB.

This project uses:

```text
AWS Load Balancer Controller
```

Flow:

```text
Internet User
    ↓
AWS ALB
    ↓
Ingress Rules
    ↓
NodePort Service
    ↓
Application Pods
```

---

## Health Probes

### Liveness Probe

Endpoint:

```http
GET /health
```

Purpose:

- Detect unhealthy containers
- Restart failed application pods

---

### Readiness Probe

Endpoint:

```http
GET /ready
```

Purpose:

- Verify application readiness
- Prevent traffic to unhealthy pods

---

## Self-Healing

Kubernetes automatically replaces failed pods.

Example:

```text
Desired replicas: 2
Actual after failure: 1
Action: Kubernetes creates replacement pod
```

---

## Scaling

Horizontal scaling is supported.

Example:

```bash
kubectl scale deployment go-app --replicas=5
```

---

## Networking

The cluster uses:

```text
Calico CNI
```

Responsibilities:

- Pod-to-pod communication
- Cross-node networking
- Service networking

---

# AWS Infrastructure Design

The application is deployed on AWS using layered infrastructure design.

---

## Infrastructure Components

AWS services used:

- Amazon EC2
- Amazon VPC
- AWS ALB
- Amazon RDS PostgreSQL
- Amazon S3
- IAM
- NAT Gateway
- Security Groups

---

## VPC Design

A custom Amazon VPC provides network isolation.

Example:

```text
10.0.0.0/16
```

Purpose:

- Isolated networking
- Controlled traffic routing
- Infrastructure segmentation

---

## Subnet Architecture

### Public Subnets

Used for:

- AWS ALB
- NAT Gateway

Characteristics:

- Internet accessible
- Public traffic entry

---

### Private Subnets

Used for:

- Kubernetes backend workloads
- Amazon RDS PostgreSQL

Characteristics:

- No direct internet exposure
- Controlled outbound access

---

## EC2 Compute Layer

Kubernetes runs on EC2 instances.

Cluster:

```text
1 Control Plane
2 Worker Nodes
```

Responsibilities:

- Host Kubernetes cluster
- Run application workloads
- Process incoming traffic

---

## Application Load Balancer

Internet-facing AWS ALB provides external application access.

Responsibilities:

- Accept HTTP traffic
- Perform health checks
- Route traffic to worker nodes

Flow:

```text
Internet User
    ↓
AWS ALB
    ↓
Worker Nodes
    ↓
NodePort Service
    ↓
Pods
```

---

## Amazon RDS PostgreSQL

Stores structured metadata including:

- User details
- KYC workflow status
- S3 document references
- Timestamps

Database placement:

```text
Private subnet
```

---

## Amazon S3

Stores uploaded KYC documents.

Benefits:

- Durable storage
- Elastic scalability
- Decoupled document persistence

---

## IAM Access

AWS API access is authenticated using application credentials provided through Kubernetes Secrets.

Current implementation:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
```

---

## Security Groups

Traffic is controlled using layered Security Group rules.

Access model:

- ALB accepts public HTTP traffic
- Worker nodes accept traffic from ALB
- PostgreSQL accepts traffic only from application layer

This limits direct backend exposure.

---

# Quick Deployment Guide

This section provides a high-level workflow for reproducing the project environment.

---

## Prerequisites

Ensure the following are available:

### AWS Infrastructure

- AWS account
- Custom VPC
- Public and private subnets
- Internet Gateway
- NAT Gateway
- Security Groups
- Amazon RDS PostgreSQL
- Amazon S3 bucket
- EC2 instances for Kubernetes cluster

---

### Kubernetes Environment

- Self-managed Kubernetes cluster using kubeadm
- 1 control plane node
- 2 worker nodes
- Calico CNI
- kubectl installed
- containerd runtime

---

### Development Tools

- Git
- Docker
- DockerHub account
- Go 1.23+

---

## Clone Repository

```bash
git clone https://github.com/Mandar-Tannu/aws_kubernetes_kubeadm_go_app.git
cd aws_kubernetes_kubeadm_go_app
```

---

## Build and Push Docker Image

Build image:

```bash
docker build -t your-dockerhub-username/go-app:v1 .
```

Login:

```bash
docker login
```

Push image:

```bash
docker push your-dockerhub-username/go-app:v1
```

---

## Create Kubernetes Secret

Create runtime secret:

```bash
kubectl create secret generic go-app-secret \
  --from-literal=RDS_DB_HOST=<your-rds-endpoint> \
  --from-literal=RDS_DB_PORT=5432 \
  --from-literal=RDS_DB_USER=<db-user> \
  --from-literal=RDS_DB_PASSWORD=<db-password> \
  --from-literal=RDS_DB_NAME=<db-name> \
  --from-literal=RDS_DB_SSLMODE=disable \
  --from-literal=S3_BUCKET_NAME=<your-s3-bucket> \
  --from-literal=AWS_ACCESS_KEY_ID=<your-access-key> \
  --from-literal=AWS_SECRET_ACCESS_KEY=<your-secret-key>
```

---

## Deploy Application

Apply manifests:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
```

---

## Access Application

Retrieve ingress:

```bash
kubectl get ingress
```

Open:

```text
http://<alb-dns-name>
```

Expected UI:

- Name field
- Email field
- Phone field
- Document upload field

---

# Operational Verification

Use the following commands to validate deployment health.

---

## Cluster Health

Check nodes:

```bash
kubectl get nodes -o wide
```

---

## Application Pods

Check pod status:

```bash
kubectl get pods -o wide
```

Expected:

```text
2 Running pods
```

---

## Deployment Status

```bash
kubectl get deployment
```

Expected:

```text
READY 2/2
```

---

## Service Verification

```bash
kubectl get svc
```

Expected:

```text
go-app-service   NodePort
```

---

## Ingress Verification

```bash
kubectl get ingress
```

Expected:

```text
ALB DNS endpoint
```

---

## Application Logs

```bash
kubectl logs deployment/go-app
```

Expected events:

- app startup
- DB connection success
- table creation
- HTTP server startup

---

## Health Endpoint

```bash
curl http://<alb-dns>/health
```

Expected:

```text
OK
```

---

## Readiness Endpoint

```bash
curl http://<alb-dns>/ready
```

Expected:

```text
HTTP 200
```

---

## Database Verification

Connect to PostgreSQL:

```bash
psql -h <rds-endpoint> -U <db-user> -d <db-name>
```

Query:

```sql
SELECT * FROM users;
```

---

## S3 Verification

Verify uploaded objects:

```text
AWS Console → S3 → Bucket → kyc-docs/
```

---

## Self-Healing Test

Delete a pod:

```bash
kubectl delete pod <pod-name>
```

Verify replacement:

```bash
kubectl get pods
```

Expected:

```text
Replacement pod created automatically
```

---

## Scaling Test

```bash
kubectl scale deployment go-app --replicas=5
```

Verify:

```bash
kubectl get pods
```

Expected:

```text
5 Running pods
```

---

# Security Considerations

This project implements foundational production-style security controls while acknowledging areas for enterprise-grade improvement.

---

## Current Security Controls

Implemented protections:

- Custom VPC network isolation
- Public/private subnet segmentation
- Security Group-based traffic control
- Private database placement
- NAT-based controlled outbound access
- Kubernetes Secret-based credential management
- Managed PostgreSQL deployment
- Application health monitoring
- Infrastructure separation across compute, storage, and database layers

---

## Current Limitations

The implementation is intentionally simplified.

Current gaps:

- Static AWS credentials
- No HTTPS / TLS encryption
- No IAM Roles for Service Accounts (IRSA)
- No S3 encryption policy enforcement
- No authentication / authorization layer
- No WAF protection
- No malware scanning for uploaded files
- No rate limiting
- No audit/security monitoring
- Single RDS instance deployment

---

## Production Improvements

Recommended enterprise enhancements:

- IRSA for temporary pod credentials
- HTTPS via AWS ACM
- AWS WAF
- S3 encryption (SSE-S3 / SSE-KMS)
- JWT / OAuth2 / Cognito authentication
- File validation and malware scanning
- Rate limiting
- centralized security monitoring
- Multi-AZ RDS deployment

---

# Future Improvements / Roadmap

Potential next-step enhancements.

---

## Infrastructure

- Migrate to Amazon EKS
- Multi-AZ deployment
- Higher availability architecture
- improved network resilience

---

## Kubernetes

- Horizontal Pod Autoscaler (HPA)
- Resource requests and limits
- Namespace isolation
- ConfigMap-based configuration
- GitOps deployment model

---

## Observability

- Prometheus monitoring
- Grafana dashboards
- centralized logging (ELK / EFK)
- distributed tracing

---

## CI/CD

- Jenkins pipeline
- GitHub Actions
- ArgoCD deployment automation
- automated testing pipeline

---

## Application

- Input validation
- duplicate user detection
- business rule enforcement
- asynchronous KYC workflow
- event-driven architecture
- document processing automation

---

## Security

- IAM least privilege
- IRSA
- HTTPS
- AWS WAF
- audit monitoring
- file scanning
- secure secret management

---

## Database

- migration tooling
- read replicas
- Multi-AZ deployment
- schema version management

---

# Project Summary

Designed and deployed a production-style cloud-native KYC document management platform using **Go**, **self-managed Kubernetes (kubeadm)**, **AWS ALB**, **Amazon RDS PostgreSQL**, and **Amazon S3**.

Implemented containerized multi-replica application deployment with Kubernetes health probes, ingress routing, secret-based runtime configuration, persistent cloud storage integration, and operational validation workflows.

This project demonstrates practical experience in:

- cloud-native backend architecture
- Kubernetes orchestration
- AWS infrastructure design
- application resilience engineering
- production-style deployment patterns
- secure infrastructure design
