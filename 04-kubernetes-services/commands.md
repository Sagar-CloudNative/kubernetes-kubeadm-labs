# ⚙️ Commands Used in Lab 04

This document contains all commands used while creating and testing **Kubernetes Services**.

---

## 1️⃣ Create a Test Pod

We first deploy a simple **NGINX pod**.

```bash
kubectl run nginx-pod --image=nginx --port=80
```

Verify the pod:

```bash
kubectl get pods
```

Example output:

```text
NAME        READY   STATUS    AGE
nginx-pod   1/1     Running   30s
```

---

## 2️⃣ Expose the Pod as a Service

Create a Kubernetes Service that exposes the pod.

```bash
kubectl expose pod nginx-pod --port=80 --target-port=80 --name=nginx-service
```

---

## 3️⃣ Verify the Service

Check the created services.

```bash
kubectl get services
```

Example output:

```text
NAME            TYPE        CLUSTER-IP      PORT(S)
kubernetes      ClusterIP   10.96.0.1       443/TCP
nginx-service   ClusterIP   10.100.50.12    80/TCP
```

---

## 4️⃣ View Service Details

Describe the service.

```bash
kubectl describe service nginx-service
```

This shows:

- Service IP
- Target pods
- Endpoints
- Ports

---

## 5️⃣ Check Service Endpoints

```bash
kubectl get endpoints
```

Example:

```text
NAME            ENDPOINTS
nginx-service   10.244.1.5:80
```

This confirms the service is correctly routing traffic to the pod.

---

## 6️⃣ Test Connectivity Inside Cluster

Run a temporary test pod:

```bash
kubectl run curl-pod --image=curlimages/curl -it --rm -- sh
```

Inside the pod run:

```bash
curl nginx-service
```

You should see the **NGINX welcome page HTML**.

---

## 🔍 Troubleshooting

Check pod status:

```bash
kubectl get pods -o wide
```

Check service details:

```bash
kubectl describe svc nginx-service
```

Check endpoints:

```bash
kubectl get endpoints
```

---

## ✅ Result

You successfully created a **Kubernetes Service** that provides stable networking and load balancing for pods.
