Jenkins Shared Library Microservices Platform
Docker + Kubernetes on Amazon EKS
This repository documents a production-style microservices DevOps platform built using:
Jenkins CI/CD
Jenkins Shared Library (central pipeline logic)
Docker & Docker Hub
Amazon EKS (Kubernetes orchestration)
GitHub Organization with team governance
Namespace-based Kubernetes isolation
This platform demonstrates:
Reusable CI/CD architecture
Microservice autonomy
Platform engineering best practices
Scalable Kubernetes deployment model
📌 What This Platform Does
It automates the full software delivery lifecycle:
Code Push → Build → Test → Containerize → Push → Deploy → Verify
All CI/CD logic is centralized in a Jenkins Shared Library, while each microservice contains only a minimal Jenkinsfile.
🏗️ Architecture Overview
Developer
   ↓
GitHub Repository
   ↓
Jenkins Pipeline (Shared Library)
   ↓
Docker Build & Push
   ↓
Amazon EKS Deployment
   ↓
Kubernetes LoadBalancer
🧱 Step-by-Step Implementation Guide
This section allows anyone to reproduce the platform.
1️⃣ Prerequisites
You must have:
AWS Account
Docker Hub Account
Jenkins server installed
kubectl installed
AWS CLI configured
2️⃣ Create EKS Cluster
Create an EKS cluster (example region: us-east-2):
aws eks create-cluster --name aquila-cluster ...
Update kubeconfig:
aws eks update-kubeconfig --region us-east-2 --name aquila-cluster
Verify:
kubectl get nodes
3️⃣ Create GitHub Organization Structure
Teams
Team	Responsibility
order-service	Owns order microservice
payment-service	Owns payment microservice
platform	Owns CI/CD & governance
Repositories
Repository	Description
order-service.java	Order service
AuthController	Authentication service
payment-service	Payment service
jenkins-shared-library	Central CI/CD logic
4️⃣ Configure Jenkins
Install Required Plugins
Pipeline
Git
Docker
Kubernetes CLI
AWS Credentials
Add Credentials in Jenkins
ID	Type	Purpose
docker-cred	Username/Password	Docker Hub login
aws-cred	IAM Access Key	EKS access
5️⃣ Jenkins Shared Library Setup
In Jenkins:
Manage Jenkins → Configure System → Global Pipeline Libraries
Add:
Name: shared-lib
Default Version: main
Repository: https://github.com/<org>/jenkins-shared-library.git
Shared Library Function
deployMicroservice(...)
This function performs:
Git checkout
Maven build & test
Docker build
Docker push
Kubernetes apply
Rolling update verification
6️⃣ Microservice Repository Structure
Each microservice contains:
.
├── Dockerfile
├── Jenkinsfile
├── k8-deployment.yaml
├── k8-service.yaml
├── pom.xml
└── src/
Minimal Jenkinsfile
@Library('shared-lib') _
deployMicroservice(
  repoUrl: 'https://github.com/org/service.git',
  imageName: 'service-name',
  namespace: 'service-namespace',
  clusterName: 'aquila-cluster'
)
7️⃣ Kubernetes Deployment Design
Each service runs in a dedicated namespace.
Service	Namespace
Auth	auth-namespace
Order	order-namespace
Payment	payment-namespace
Create namespace automatically in pipeline:
kubectl create ns <namespace>
Service Exposure
Currently using:
type: LoadBalancer
This provisions an AWS ELB automatically.
8️⃣ Deployment Workflow
Developer Push
      ↓
GitHub Webhook
      ↓
Jenkins Job Trigger
      ↓
Shared Library Pipeline
      ↓
Docker Image Push
      ↓
kubectl Apply
      ↓
Rolling Update
Rollout verification:
kubectl rollout status deployment/<name>
🛡️ Security Model
Namespace isolation
IAM credentials stored securely in Jenkins
No hardcoded secrets in repos
Separate team permissions per repository
📈 Platform Engineering Highlights
Centralized CI/CD governance
Shared pipeline standardization
Service-level autonomy
Scalable Kubernetes deployment
Rolling updates with zero downtime
Multi-service architecture ready for growth
🔮 Recommended Production Improvements
AWS Load Balancer Controller with Ingress
GitOps integration (ArgoCD / Flux)
Prometheus & Grafana monitoring
HPA autoscaling
Karpenter node autoscaling
Blue/Green or Canary deployments
External secrets integration
👤 Author
Aquila Kuunyangna
DevOps Engineer | AWS Certified | Platform Engineering Enthusiast
