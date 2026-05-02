# 🚀 Blue-Green Deployment in Kubernetes

This project demonstrates **Blue-Green Deployment in Kubernetes** using a simple NGINX application.  
You will learn how to deploy two versions of an application, switch traffic instantly, and perform rollback without downtime.

> ⚠️ This demo works across most Kubernetes versions.

---

## 🧱 Architecture Overview

```
                +----------------------+
                |      User Traffic    |
                +----------+-----------+
                           |
                           v
                  +------------------+
                  |   Kubernetes     |
                  |     Service      |
                  +--------+---------+
                           |
        -----------------------------------------
        |                                       |
        v                                       v
+---------------------+              +---------------------+
|   Blue Deployment   |              |  Green Deployment   |
|   (NGINX 1.25)      |              |   (NGINX 1.27)      |
|   version: blue     |              |   version: green    |
+---------------------+              +---------------------+

Before Switch  → Traffic → Blue  
After Switch   → Traffic → Green
```

---

## 📂 Project Structure

```
blue-green-kubernetes-demo/
├── README.md
├── blue-deployment.yaml
├── green-deployment.yaml
├── service.yaml
```

---

## ⚙️ Prerequisites

- Kubernetes cluster (Minikube / Kubeadm / Cloud)
- kubectl configured
- Basic understanding of Pods and Services

---

## 🚀 Step-by-Step Implementation

### 1️⃣ Deploy Blue Version

```bash
kubectl apply -f blue-deployment.yaml
```

---

### 2️⃣ Create Service

```bash
kubectl apply -f service.yaml
```

Check service:

```bash
kubectl get svc
```

---

### 3️⃣ Access Application

```bash
kubectl get nodes -o wide
```

Open in browser:

```
http://NODE-IP:30007
```

👉 At this stage, traffic is routed to **Blue environment**

---

### 4️⃣ Deploy Green Version

```bash
kubectl apply -f green-deployment.yaml
```

---

### 5️⃣ Verify Pods

```bash
kubectl get pods -o wide --show-labels
```

👉 Both Blue and Green pods should be running

---

### 6️⃣ Verify Service Endpoints (Before Switch)

```bash
kubectl get endpoints myapp-service
```

👉 Endpoints will point to **Blue pods**

---

### 7️⃣ Switch Traffic to Green

```bash
kubectl edit svc myapp-service
```

Change:

```yaml
version: blue
```

To:

```yaml
version: green
```

👉 Traffic is now routed to **Green environment**

---

### 8️⃣ Verify Traffic Switch

```bash
kubectl get endpoints myapp-service
```

👉 Endpoints should now point to **Green pods**

---

### 9️⃣ Rollback (Optional)

```bash
kubectl edit svc myapp-service
```

Change back:

```yaml
version: blue
```

👉 Traffic switches back to **Blue environment instantly**

---

## 🧹 Cleanup

```bash
kubectl delete -f blue-deployment.yaml
kubectl delete -f green-deployment.yaml
kubectl delete -f service.yaml
```

---

## 📺 YouTube Tutorial

Watch the full step-by-step demo here:  
👉 ([Blue-Green Deployment in Kubernetes](https://youtu.be/XK8SG9TiN-I))

---

## ⭐ Key Takeaways

- Blue-Green enables **zero downtime deployments**
- Traffic switching is done using **Service selectors**
- Instant rollback is possible
- Widely used in production systems

---

## 🙌 Support

If you found this helpful:

- ⭐ Star this repository  
- 👍 Like the video  
- 🔔 Subscribe for more DevOps tutorials

---

[⬅️ Previous](../08-kubernetes-etcd-backup-restore) | [🏠 Home](../README.md) | [Next Lab: Canary Deployment ➡️](../10-kubernetes-canary-deployment)
