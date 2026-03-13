# ⚙️ Commands Used in Lab 04

This document lists all commands used to demonstrate Kubernetes scheduling behavior.

---

# 1️⃣ Verify Cluster

```bash
kubectl get nodes
```

---

# 2️⃣ Check Control Plane Taint

```bash
kubectl describe node k8s-master | grep -i taint
```

Expected:

```
node-role.kubernetes.io/control-plane:NoSchedule
```

---

# 3️⃣ Taint Worker Node

```bash
kubectl taint nodes k8s-worker dedicated=database:NoSchedule
```

Verify:

```bash
kubectl describe node k8s-worker | grep -i taint
```

---

# 4️⃣ Deploy Pod Without Toleration

```bash
kubectl run test-pod --image=nginx
```

Check placement:

```bash
kubectl get pods -o wide
```

Pod should run on **k8s-worker2**.

---

# 5️⃣ Deploy Pod With Toleration

```bash
kubectl apply -f tolerant-pod.yaml
```

Verify:

```bash
kubectl get pods -o wide
```

---

# 6️⃣ Remove Demo Pods

```bash
kubectl delete pod test-pod tolerant-pod
```

---

# 7️⃣ Remove Worker Taint

```bash
kubectl taint nodes k8s-worker dedicated=database:NoSchedule-
```

---

# 8️⃣ Add Node Labels

```bash
kubectl label node k8s-worker disktype=hdd
kubectl label node k8s-worker2 disktype=ssd
```

Verify:

```bash
kubectl get nodes -L disktype
```

---

# 9️⃣ Deploy Pod Using Node Affinity

```bash
kubectl apply -f affinity-pod.yaml
```

Check placement:

```bash
kubectl get pods -o wide
```

Expected: Pod runs on **k8s-worker2**.

---

# 🔟 Cleanup Affinity Pod

```bash
kubectl delete pod affinity-pod
```

Remove labels:

```bash
kubectl label node k8s-worker disktype-
kubectl label node k8s-worker2 disktype-
```

---

# 1️⃣1️⃣ Deploy Pod Anti-Affinity Example

```bash
kubectl apply -f web-deploy.yaml
```

Check scheduling:

```bash
kubectl get pods -o wide
```

Pods should run on **different worker nodes**.

---

# 1️⃣2️⃣ Test Scaling

Edit deployment:

```
replicas: 3
```

Apply again:

```bash
kubectl apply -f web-deploy.yaml
```

Verify:

```bash
kubectl get pods -o wide
```

Third pod should remain **Pending**.

---

# 1️⃣3️⃣ Inspect Pending Pod

```bash
kubectl describe pod <pending-pod>
```

---

# 1️⃣4️⃣ Cleanup

```bash
kubectl delete deployment web-app
```

---

# ✅ Result

You demonstrated how Kubernetes scheduler evaluates:

- Taints
- Tolerations
- Node Affinity
- Pod Anti-Affinity

These mechanisms are widely used in **production Kubernetes clusters**.
