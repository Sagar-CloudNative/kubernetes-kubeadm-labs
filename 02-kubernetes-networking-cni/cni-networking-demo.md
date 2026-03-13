# CNI Networking Validation Demo

This lab validates that the Kubernetes network fabric is functioning correctly using the Flannel CNI plugin. Follow these steps to verify your cluster's health.

---

## Step 1 — Verify CNI Plugin Status

The CNI runs as a DaemonSet, meaning one pod should exist on every node in the cluster.

```bash
kubectl get pods -n kube-flannel
```

**Expected Result:**
You should see a `Running` status for a Flannel pod on both the master and the worker.

```text
NAME                    READY   STATUS    RESTARTS   AGE
kube-flannel-ds-xxxxx   1/1     Running   0          5m
kube-flannel-ds-yyyyy   1/1     Running   0          5m
```

---

## Step 2 — Verify Node Readiness

Nodes remain in `NotReady` status until the CNI plugin is active.

```bash
kubectl get nodes
```

**Expected Result:**

```text
NAME         STATUS   ROLES           AGE   VERSION
k8s-master   Ready    control-plane   20m   v1.29.x
k8s-worker   Ready    <none>          15m   v1.29.x
```

---

## Step 3 — Verify Core System Services

CoreDNS is the internal DNS provider for Kubernetes. It will stay in a `Pending` state until networking is configured.

```bash
kubectl get pods -n kube-system
```

**Expected Result:**
Verify that `coredns` pods have moved to the `Running` state.

---

## Step 4 — Create Test Pods

We will deploy two lightweight pods to test the virtual network.

```bash
# Deploy NGINX (Server)
kubectl run pod-a --image=nginx --restart=Never

# Deploy BusyBox (Client)
kubectl run pod-b --image=busybox --restart=Never -- sleep 3600
```

---

## Step 5 — Inspect Pod IP Assignment

Each Pod in Kubernetes must receive a unique IP address from the Pod CIDR range.

```bash
kubectl get pods -o wide
```

**Expected Result:**
Each pod will have a unique IP (e.g., `10.244.1.2` and `10.244.1.3`).

---

## Step 6 — Test Cross-Pod Communication

We will now enter `pod-b` and attempt to reach `pod-a` over the network.

**1. Identify the IP of Pod A:**

```bash
kubectl get pod pod-a -o wide
```

**2. Execute a shell inside Pod B:**

```bash
kubectl exec -it pod-b -- sh
```

**3. Ping Pod A using its IP:**

```bash
ping <POD-A-IP>
```

**Expected Result:**

```text
64 bytes from 10.244.1.2: seq=0 ttl=64 time=1.1 ms
64 bytes from 10.244.1.2: seq=1 ttl=64 time=0.06 ms
```
*(Successful pings confirm that the Flannel CNI is correctly routing traffic between containers.)*

**4. Exit the pod:**

```bash
exit
```

---

## Summary of Results

By completing this demo, you have successfully validated:
* CNI Installation: The Flannel DaemonSet is active.
* Node Readiness: Nodes have transitioned to `Ready`.
* IPAM: Kubernetes successfully assigned unique IPs to Pods.
* Connectivity: Pod-to-Pod communication is fully operational.

---
| [« Lab 01: Cluster Setup](../01-kubeadm-cluster-setup/README.md) | [Main Directory](../../README.md) | [Lab 03: Cluster Expansion »](../03-multi-node-cluster-expansion/README.md) |
| :--- | :---: | ---: |
