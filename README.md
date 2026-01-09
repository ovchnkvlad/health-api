# Health API — Kubernetes Demo Project
### This project demonstrates a simple FastAPI application deployed to Kubernetes (Minikube) using production-like best practices.
The goal of this project is to showcase:
- Containerization with Docker
- Kubernetes deployment patterns
- Database management with persistent storage
- Autoscaling using HPA
- Monitoring and observability with Prometheus & Grafana

## Architecture Overview
### Components
- FastAPI — REST API with health and database checks
- PostgreSQL — Stateful database using StatefulSet + PVC
- Kubernetes (Minikube) — local Kubernetes cluster
- Ingress (NGINX) — external access to the API
- HPA — Horizontal Pod Autoscaler
- Prometheus — metrics collection
- Grafana — metrics visualization

## High-level Architecture
Client
  ↓
Ingress (NGINX)
  ↓
FastAPI Service
  ↓
PostgreSQL (StatefulSet + PVC)
## API Endpoints

| Endpoint | Endpoint |
| ------ | ------ |
| / | Root endpoint |
| /health | Application health check |
| /db | Database connectivity check |
| /metrics | Prometheus application metrics |

## Local Setup
### Prerequisites
- Docker
- Minikube
- kubectl
- Helm

### Start Minikube
```sh
minikube start
minikube addons enable ingress
```

### Build Docker Image (inside Minikube)
```sh
eval $(minikube docker-env)
docker build -t health-api:latest .
```
### Deploy Application
```sh
kubectl create namespace app-namespace
kubectl apply -f k8s/ -n app-namespace
```
### Verify Pods
```sh
kubectl get pods -n app-namespace
```
### All pods should be in Running state.

### Database
PostgreSQL is deployed using a StatefulSet with a PersistentVolumeClaim (PVC), ensuring data persistence across pod restarts and rescheduling.
### Autoscaling
The API deployment is configured with a Horizontal Pod Autoscaler (HPA) based on CPU utilization.
```sh
kubectl get hpa -n app-namespace
```

## Monitoring & Observability
### Prometheus & Grafana
Monitoring is implemented using Prometheus and Grafana via the kube-prometheus-stack Helm chart.
```sh
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```
```sh
kubectl create namespace monitoring
```
```sh
helm install monitoring prometheus-community/kube-prometheus-stack \
  -n monitoring
```
## Application Metrics
The FastAPI application exposes Prometheus metrics via the /metrics endpoint.
Prometheus scrapes application metrics using a ServiceMonitor, enabling visibility into:
- HTTP request rate
- Request latency
- HTTP status codes
### Access Grafana
```sh
kubectl port-forward svc/monitoring-grafana 3000:80 -n monitoring
```
### Grafana UI:
```sh
URL: http://localhost:3000
Username: admin
Password:
```
```sh
kubectl get secret monitoring-grafana \
  -n monitoring \
  -o jsonpath="{.data.admin-password}" | base64 --decode
```
### Key Kubernetes Concepts Demonstrated
- Deployments & Services
- StatefulSets & Persistent Volumes
- ConfigMaps & Secrets
- Ingress
- Horizontal Pod Autoscaler
- Prometheus ServiceMonitor
- Observability with Grafana

### Notes
This project is designed as a demonstration / test assignment and focuses on Kubernetes architecture and best practices rather than business logic complexity.
### Possible Improvements
- Add Alertmanager alert rules
- Add readiness and liveness probes
- Add NetworkPolicies
- Add CI/CD pipeline
- Add authentication and authorizati on