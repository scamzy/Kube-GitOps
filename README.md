# Kubernetes GitOps Project using ArgoCD

## Overview

This project demonstrates a **GitOps-based Kubernetes deployment pipeline** using:

* **K3s Kubernetes cluster**
* **ArgoCD GitOps controller**
* **GitHub as the source of truth**
* **Nginx application deployed automatically**

The project shows how changes committed to a Git repository automatically deploy and update applications in Kubernetes.

# Architecture
Developer (RHEL VM)
        |
        | git push
        v
GitHub Repository
        |
        | watched by
        v
ArgoCD GitOps Controller
        |
        | applies manifests
        v
Kubernetes Cluster (K3s on AWS EC2)
        |
        | creates
        v
Pods (nginx containers)
        |
        | exposed via
        v
Kubernetes Service (NodePort 30080)
        |
        v
Browser Access
http://3.110.184.69:30080

# Technologies Used

| Technology        | Purpose                            |
| ----------------- | ---------------------------------- |
| K3s               | Lightweight Kubernetes cluster     |
| ArgoCD            | GitOps deployment controller       |
| GitHub            | Git repository and source of truth |
| Docker Containers | Application runtime                |
| AWS EC2           | Infrastructure hosting the cluster |
| Nginx             | Example application                |

# Infrastructure Setup

## EC2 Instance
Instance type used: t3.small
Operating system: Amazon Linux

The Kubernetes cluster runs directly on this EC2 instance.

# Kubernetes Cluster Setup
K3s was installed to create a lightweight Kubernetes cluster.

Install command:
 in bash
curl -sfL https://get.k3s.io | sh -

Verify installation:
 in bash
kubectl get nodes

Example output:
NAME               STATUS   ROLES
ip-172-31-45-20    Ready    control-plane


# ArgoCD Installation

A dedicated namespace was created for ArgoCD:
 in bash
kubectl create namespace argocd

ArgoCD installation:
 in bash
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Check pods:
 in bash
kubectl get pods -n argocd

Access ArgoCD dashboard using port-forward:
 in bash
kubectl port-forward svc/argocd-server -n argocd 8080:443

ArgoCD UI:
https://<EC2-IP>:8080


# Git Repository Structure
Kube-GitOps
│
├── app
│   └── nginx
│       ├── deployment.yaml
│       └── service.yaml
│
├── argocd
│
└── k8s

# Kubernetes Deployment

The application is deployed using a Kubernetes Deployment.
File:
app/nginx/deployment.yaml

Key configuration:
replicas: 3
image: nginx
containerPort: 80

# Kubernetes Service

To expose the application externally, a NodePort service is used.
File:
app/nginx/service.yaml

Configuration:
type: NodePort
nodePort: 30080

This exposes the application on:

http://<EC2-IP>:30080


# GitOps Workflow
Edit YAML  →  Commit  →  Push to GitHub
      |            |            |
      v            v            v
Developer      GitHub Repo     ArgoCD detects change
                                      |
                                      v
                           Kubernetes cluster updated
                                      |
                                      v
                              Application deployed

Example:
replicas: 1 → replicas: 3

After pushing the change:
git commit
git push

ArgoCD automatically scaled the application.

# Verification

Check running pods:
in bash
kubectl get pods

Example output:
nginx-xxxx Running
nginx-xxxx Running
nginx-xxxx Running

Access the application:
http://<EC2-IP>:30080

Expected result:
Welcome to nginx!

# Project Highlights

✔ GitOps-based Kubernetes deployment
✔ Automatic synchronization between Git and cluster
✔ Infrastructure running on AWS EC2
✔ Lightweight Kubernetes using K3s
✔ Application exposed using Kubernetes Service

# Future Improvements

Possible improvements:

* Add Ingress controller
* Deploy multiple environments (dev / prod)
* Use Helm charts with ArgoCD
* Implement CI/CD pipelines
* Deploy monitoring stack (Prometheus + Grafana)

# Learning Outcomes

This project demonstrates practical experience with:
* Kubernetes fundamentals
* GitOps workflows
* ArgoCD deployment automation
* Kubernetes services and networking
* Cloud infrastructure setup

