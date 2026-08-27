# Kubernetes Deployment Guide

This directory contains the Kubernetes manifests used to deploy the ecommerce platform.

## Architecture

```text
                         Internet
                            |
                            v
                    NGINX Load Balancer
                            |
                            v
                     Ingress NGINX
                       /        \
                      /          \
                     v            v
              Frontend Service   Backend Service
                     |            |
                     v            v
                Angular App    Spring Boot API
                                  |
                           +------+------+
                           |             |
                           v             v
                       MariaDB        Redis
                                      Sentinel
                                        |
                                        v
                                      NFS
```

## Directory Structure

```text
k8s/
├── namespace.yaml
│
├── backend/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── configmap.yaml
│   └── secret.example.yaml
│
├── frontend/
│   ├── deployment.yaml
│   └── service.yaml
│
├── ingress/
│   ├── frontend-ingress.yaml
│   └── backend-ingress.yaml
│
├── autoscaling/
│   └── backend-hpa.yaml
│
├── storage/
│   ├── pv.example.yaml
│   └── pvc.yaml
│
└── redis/
    ├── values.yaml
    ├── pvc.yaml
    ├── service.yaml
    ├── headless-service.yaml
    └── README.md
```

## Prerequisites

The Kubernetes cluster should have:

* Kubernetes
* Ingress NGINX
* NFS StorageClass
* MariaDB
* Redis Sentinel
* Kubernetes Metrics Server for HPA
* Required Kubernetes Secrets
* Required ConfigMaps

## Deployment Order

Apply the manifests in the following order.

### 1. Namespace

```bash
kubectl apply -f k8s/namespace.yaml
```

### 2. Storage

```bash
kubectl apply -f k8s/storage/pvc.yaml
```

If a static PV is required, create it from the example:

```bash
kubectl apply -f k8s/storage/pv.example.yaml
```

Review and customize the NFS server/path before applying the example PV.

### 3. Backend configuration

Create the required Secret from the example:

```bash
cp k8s/backend/secret.example.yaml /tmp/ecommerce-backend-secret.yaml
```

Edit the values before applying:

```bash
nano /tmp/ecommerce-backend-secret.yaml
```

Then:

```bash
kubectl apply -f /tmp/ecommerce-backend-secret.yaml
kubectl apply -f k8s/backend/configmap.yaml
```

Do not commit real credentials to Git.

### 4. Backend

```bash
kubectl apply -f k8s/backend/deployment.yaml
kubectl apply -f k8s/backend/service.yaml
```

Check:

```bash
kubectl -n ecommerce get pods
kubectl -n ecommerce get svc
```

### 5. Frontend

```bash
kubectl apply -f k8s/frontend/deployment.yaml
kubectl apply -f k8s/frontend/service.yaml
```

Check:

```bash
kubectl -n ecommerce get pods
kubectl -n ecommerce get svc
```

### 6. Ingress

```bash
kubectl apply -f k8s/ingress/frontend-ingress.yaml
kubectl apply -f k8s/ingress/backend-ingress.yaml
```

Check:

```bash
kubectl -n ecommerce get ingress
```

### 7. Backend HPA

Metrics Server must be available before applying the HPA.

```bash
kubectl apply -f k8s/autoscaling/backend-hpa.yaml
```

Check:

```bash
kubectl -n ecommerce get hpa
kubectl top pods -n ecommerce
```

## Verification

Check all resources:

```bash
kubectl -n ecommerce get all
```

Check persistent volumes:

```bash
kubectl get pv
kubectl -n ecommerce get pvc
```

Check ingress:

```bash
kubectl -n ecommerce get ingress
```

Check HPA:

```bash
kubectl -n ecommerce get hpa
```

Check backend logs:

```bash
kubectl -n ecommerce logs \
  deployment/backend-ecommerce-deployment \
  --tail=100
```

Check frontend logs:

```bash
kubectl -n ecommerce logs \
  deployment/frontend-ecommerce-deployment \
  --tail=100
```

## Rollout Verification

Backend:

```bash
kubectl -n ecommerce rollout status \
  deployment/backend-ecommerce-deployment
```

Frontend:

```bash
kubectl -n ecommerce rollout status \
  deployment/frontend-ecommerce-deployment
```

## Rollback

If a deployment fails:

```bash
kubectl -n ecommerce rollout history \
  deployment/backend-ecommerce-deployment
```

Rollback:

```bash
kubectl -n ecommerce rollout undo \
  deployment/backend-ecommerce-deployment
```

The same approach can be used for the frontend deployment.

## Container Images

Current application images:

```text
Frontend:
truonghuy0108/frontend-ecommerce

Backend:
truonghuy0108/ecommerce-backend
```

The backend currently uses a versioned image tag.

The frontend should also use immutable version tags instead of `latest` for production-style deployments.

Recommended format:

```text
frontend-ecommerce:<version>
ecommerce-backend:<version>
```

For example:

```text
frontend-ecommerce:1.0.1
ecommerce-backend:1.0.1
```

Or use Git commit identifiers:

```text
frontend-ecommerce:git-8f32a1c
ecommerce-backend:git-8f32a1c
```

## Security

Never commit:

* Real database passwords
* Redis passwords
* Kubernetes admin kubeconfig
* TLS private keys
* Production credentials

Use `secret.example.yaml` as a template only.

## Troubleshooting

Useful commands:

```bash
kubectl get nodes
kubectl get pods -A
kubectl -n ecommerce describe pod <pod-name>
kubectl -n ecommerce logs <pod-name>
kubectl -n ecommerce get events --sort-by=.lastTimestamp
```

For networking:

```bash
kubectl -n ecommerce get svc
kubectl -n ecommerce get ingress
kubectl get endpoints -n ecommerce
```

For storage:

```bash
kubectl get pv
kubectl get pvc -A
kubectl get storageclass
```
