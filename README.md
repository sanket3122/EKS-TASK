# 🌩️ EKS Cluster with WordPress & MySQL on AWS

This repository contains `eksctl` and Kubernetes YAML manifests to create an **Amazon EKS cluster** and deploy a **WordPress frontend** with a **MySQL backend**.  
The `images/` folder has AWS Console and `kubectl` output screenshots for each major step so you can visually verify the infrastructure.

---

## 🧱 What this configuration creates

Using `eksctl`, Kustomize, and Kubernetes manifests, this setup provisions:

- **EKS Cluster** named `s3cluster` in `ap-south-1`
- **Three node groups**:
  - `ng1` – `t2.micro`, desired capacity **4**
  - `ng2` – `t2.small`, desired capacity **3**
  - `ng3` – `t2.medium`, desired capacity **4**
- **Kustomize configuration** that:
  - Generates a Kubernetes **Secret** `mysql-pass` with the MySQL root password
  - Applies `mysql.yaml` and `wordpress.yaml`
- **MySQL backend**:
  - `wordpress-mysql` **Deployment**
  - `wordpress-mysql` **Service** (ClusterIP, port `3306`)
  - `mysql-pvc` **PersistentVolumeClaim** with `10Gi` storage
- **WordPress frontend**:
  - `wordpress` **Deployment**
  - `wordpress` **Service** of type `LoadBalancer` (port `80`)
  - `wp-pvc` **PersistentVolumeClaim** with `10Gi` storage
- End-to-end **WordPress blog** running on EKS with persistent storage and an external load balancer

This repo is a simple but complete example of running a **stateful application (WordPress + MySQL)** on **Amazon EKS** using declarative configs.

---

## 🧰 Prerequisites

- AWS account with permissions to create:
  - EKS cluster, node groups, EC2 instances, networking resources, and load balancers
- Tools installed:
  - `awscli`
  - `eksctl`
  - `kubectl`
- (Optional) Kustomize support (built into recent `kubectl` via `kubectl apply -k`)
- AWS credentials configured:
  - `aws configure`, or  
  - environment variables, or  
  - named AWS profile

---

## 🚀 How to use

### 1) Clone the repository

```bash
git clone https://github.com/sanket3122/EKS-TASK.git
cd EKS-TASK
```

2) Configure AWS credentials
```bash
aws configure
```
Or set the environment variables:
```
export AWS_ACCESS_KEY_ID=YOUR_KEY
export AWS_SECRET_ACCESS_KEY=YOUR_SECRET
export AWS_DEFAULT_REGION=ap-south-1
```

3) Create the EKS cluster with node groups
```
eksctl create cluster -f cluster.yml
```

4) Verify the cluster and nodes
```
kubectl get nodes
kubectl get ns
```

5) Deploy MySQL and WordPress using Kustomize
```
kubectl apply -k .
# or, individually:
# kubectl apply -f mysql.yaml
# kubectl apply -f wordpress.yaml
```

6) Check pods and services
```
kubectl get pods
kubectl get svc
```

7) Get the external LoadBalancer endpoint for WordPress:
```
kubectl get svc wordpress -o wide
```

Open the external hostname/IP in a browser to access the WordPress site.

Destroy resources when finished
```
kubectl delete -k .
# or:
# kubectl delete -f wordpress.yaml -f mysql.yaml

eksctl delete cluster -f cluster.yml
# or:
# eksctl delete cluster --name s3cluster --region ap-south-1
```

## Files

`cluster.yml` – eksctl config for the EKS cluster and node groups
`kustomization.yml` – Kustomize config to generate the MySQL secret and apply resources
`mysql.yaml` – Kubernetes Service, PVC, and Deployment for the MySQL backend
`wordpress.yaml` – Kubernetes Service, PVC, and Deployment for the WordPress frontend

images/ – All AWS Console and kubectl screenshots referenced below

High-level views (cluster, nodes, nodegroups, workloads, outputs):





