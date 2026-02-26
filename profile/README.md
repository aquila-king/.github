 Jenkins Shared Library Microservices Platform with Docker & Amazon EKS

This project demonstrates a **production-style microservices DevOps platform** built using:

* **Jenkins CI/CD**
* **Jenkins Shared Library (central pipeline logic)**
* **Docker Hub (container registry)**
* **Amazon EKS (Kubernetes orchestration)**
* **GitHub Organization with team-based governance**
* **Namespace isolation for multi-service deployments**

The platform showcases **reusable CI/CD architecture**, **microservice autonomy**, and **platform engineering best practices**.

---

 Overview

The system automates the software delivery lifecycle from **code commit → containerization → deployment → Kubernetes orchestration**.

## Pipeline Stages

1. **Checkout** – Jenkins pulls code from GitHub
2. **Build** – Maven compiles and packages the application
3. **Test** – Unit tests executed (optional skip)
4. **Docker Build** – Container image created
5. **Docker Push** – Image pushed to Docker Hub
6. **Deploy to EKS** – Kubernetes manifests applied
7. **Rolling Update** – Deployment image updated and rollout verified

---

# GitHub Organization Architecture

## Teams

| Team            | Responsibility                                  |
| --------------- | ----------------------------------------------- |
| order-service   | Owns order microservice                         |
| payment-service | Owns payment microservice                       |
| platform        | Owns shared CI/CD infrastructure and governance |

This structure enables:

* Ownership boundaries
* Access control
* Platform governance
* Reduced blast radius

---

# Repositories

| Repository             | Description                           |
| ---------------------- | ------------------------------------- |
| order-service.java     | Order microservice                    |
| AuthController         | Authentication microservice           |
| payment-service        | Payment microservice                  |
| jenkins-shared-library | Central reusable CI/CD pipeline logic |

---

# Jenkins Shared Library Architecture

The shared library provides a reusable function:

```groovy
deployMicroservice()
```

This function performs:

* Repository checkout
* Maven build & test
* Docker build & push
* Kubernetes deployment
* Rollout verification

### Benefits

* Eliminates duplicated Jenkinsfiles
* Standardizes CI/CD across services
* Enables platform governance
* Simplifies onboarding of new microservices

---

# Microservice Repository Structure

Each service repository contains:

```
.
├── Dockerfile
├── Jenkinsfile (minimal)
├── k8-deployment.yaml
├── k8-service.yaml
├── pom.xml
└── application source
```

The Jenkinsfile simply calls the shared library:

```groovy
@Library('shared-lib') _
deployMicroservice(...)
```

---

# Containerization

Each service uses Docker for packaging.

Example Dockerfile:

```
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
```

Images are pushed to Docker Hub for deployment.

---

# Kubernetes Deployment Strategy (Amazon EKS)

Each microservice is deployed to a **dedicated namespace** for isolation.

| Service | Namespace         |
| ------- | ----------------- |
| Auth    | auth-namespace    |
| Order   | order-namespace   |
| Payment | payment-namespace |

## Benefits

* Fault isolation
* Resource governance
* Independent scaling
* Secure multi-tenant cluster design

Services are exposed using **LoadBalancer service type** (AWS ELB).

---

# Jenkins Credentials

Configure the following credentials in Jenkins:

| Credential  | Purpose                              |
| ----------- | ------------------------------------ |
| docker-cred | Docker Hub login                     |
| aws-cred    | AWS IAM access for EKS               |
| kubeconfig  | Kubernetes cluster access (optional) |

---

# Deployment Workflow

```
Developer Push → GitHub Repo → Jenkins Trigger
→ Shared Library Pipeline
→ Docker Hub Image Push
→ Amazon EKS Deployment
→ LoadBalancer Exposure
```

---

# Platform Engineering Highlights

* Centralized CI/CD governance
* Microservice team autonomy
* Namespace-based Kubernetes isolation
* Reusable pipeline architecture
* Rolling deployment automation
* Multi-service platform scalability

---

# Future Enhancements

* AWS Load Balancer Controller with Ingress
* GitOps integration (ArgoCD / Flux)
* Canary / Blue-Green deployments
* HPA & Karpenter autoscaling
* External secrets integration
* Service mesh (Istio / Linkerd)

---

# Author

**Aquila Kuunyangna**
DevOps Engineer | AWS Certified | Platform Engineering Enthusiast
