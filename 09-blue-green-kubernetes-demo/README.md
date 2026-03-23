# 🚀 Blue-Green Deployment in Kubernetes

This project demonstrates **Blue-Green Deployment in Kubernetes** using a simple NGINX application.

It shows how to:
- Deploy two versions of an application (Blue & Green)
- Route traffic using a Kubernetes Service
- Switch traffic instantly without downtime
- Perform quick rollback

> ⚠️ This demo works across most Kubernetes versions.

---

## 🧱 Architecture Overview

- **Blue Deployment** → Current production version (NGINX 1.25)
- **Green Deployment** → New version (NGINX 1.27)
- **Service** → Routes traffic using label selectors

---

## 📂 Files in this Repository

- `blue-deployment.yaml` → Blue environment
- `green-deployment.yaml` → Green environment
- `service.yaml` → Service for traffic routing

---

## ⚙️ Steps to Run

### 1️⃣ Deploy Blue Version

```bash
kubectl apply -f blue-deployment.yaml
