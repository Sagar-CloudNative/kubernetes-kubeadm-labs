# 🧪 Kubernetes Service Demo

This lab demonstrates how a **Kubernetes Service provides a stable endpoint for pods**.

---

# Step 1 — Deploy a Test Pod

Create an NGINX pod.

```bash
kubectl run nginx-pod --image=nginx --port=80
```

Verify pod status.

```bash
kubectl get pods
```

Wait until the pod shows **STATUS = Running**.

---

# Step 2 — Create a Service

Expose the pod using a Kubernetes Service.

```bash
kubectl expose pod nginx-pod --port=80 --target-port=80 --name=nginx-service
```

This creates a **ClusterIP Service**.

---

# Step 3 — Verify the Service

Check the created service.

```bash
kubectl get svc
```

Example output:

```text
NAME            TYPE        CLUSTER-IP
kubernetes      ClusterIP   10.96.0.1
nginx-service   ClusterIP   10.100.50.12
```

The **ClusterIP** is the internal IP used by other pods.

---

# Step 4 — Check Service Endpoints

```bash
kubectl get endpoints
```

Example:

```text
nginx-service   10.244.1.5:80
```

This confirms the service is routing traffic to the pod.

---

# Step 5 — Test Service Connectivity

Launch a temporary pod for testing.

```bash
kubectl run curl-pod --image=curlimages/curl -it --rm -- sh
```

Inside the pod run:

```bash
curl nginx-service
```

You should see the **NGINX welcome page**.

---

# Step 6 — Verify Pod Placement

Check which node the pod is running on.

```bash
kubectl get pods -o wide
```

Example:

```text
NAME        NODE
nginx-pod   k8s-worker1
```

Even if the pod moves to another node, the **Service IP remains constant**.

---

# 🎯 Result

You learned how Kubernetes Services:

- Provide a **stable virtual IP**
- Route traffic to pods
- Enable service discovery within the cluster

This is the foundation for **microservice communication in Kubernetes**.

---

| [« Lab 03: Add Worker Node](../03-add-worker-node/README.md) | [Main Directory](../../README.md) | [Lab 05: Deployments »](../05-kubernetes-deployments/README.md) |
| :--- | :---: | ---: |
