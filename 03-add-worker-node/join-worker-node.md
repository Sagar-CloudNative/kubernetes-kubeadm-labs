# 🔗 Joining a Worker Node to a Kubernetes Cluster

In this lab we will add a **new worker node** to an existing Kubernetes cluster using the `kubeadm join` command.

This is a common real-world operation when scaling Kubernetes clusters to handle more workloads.

---

## 🎯 Lab Goal

By the end of this lab you will learn:

- How Kubernetes worker nodes securely join a cluster
- How the `kubeadm join` process works
- How to verify node registration in the cluster
- How to confirm the cluster is healthy after adding nodes

---

## 📋 Prerequisites

Before starting this lab:

- Kubernetes control plane must already be running
- At least **one worker node should already exist**
- Networking (CNI plugin) must be installed
- The new worker node must have completed **node preparation steps**

If you have not completed these labs yet:

1. **Lab 01 — Cluster Setup**
2. **Lab 02 — Kubernetes Networking**

---

# 🧩 Step 1 — Generate the Join Command

Login to the **control plane (master) node**.

Generate the join command using:

```bash
kubeadm token create --print-join-command
```

Example output:

```text
kubeadm join 192.168.56.115:6443 \
--token abcdef.1234567890abcdef \
--discovery-token-ca-cert-hash sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

This command contains:

- API server address
- Authentication token
- CA certificate hash for secure verification

Copy this entire command.

---

# 🧩 Step 2 — Run Join Command on Worker Node

Login to the **new worker node** that you want to add to the cluster.

Run the join command that was generated earlier.

Example:

```bash
sudo kubeadm join 192.168.56.115:6443 \
--token abcdef.1234567890abcdef \
--discovery-token-ca-cert-hash sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

During this process the worker node will:

1. Connect to the Kubernetes API server
2. Verify the cluster certificate
3. Download cluster configuration
4. Register itself as a node in the cluster
5. Start the kubelet service

If the process completes successfully you should see output similar to:

```text
This node has joined the cluster:

* Certificate signing request sent to apiserver
* The kubelet was informed of the new secure connection details
```

---

# 🧩 Step 3 — Verify Node Registration

Return to the **control plane node**.

Check the list of nodes in the cluster:

```bash
kubectl get nodes
```

Example output:

```text
NAME           STATUS   ROLES           AGE
k8s-master     Ready    control-plane   1h
k8s-worker1    Ready    <none>          55m
k8s-worker2    Ready    <none>          2m
```

If the new node appears with **STATUS = Ready**, the node has successfully joined the cluster.

---

# 🧩 Step 4 — Verify Kubernetes System Pods

Check the Kubernetes system pods to ensure cluster components are running normally.

```bash
kubectl get pods -n kube-system
```

Example output:

```text
NAME                                READY   STATUS    RESTARTS
coredns-xxxx                        1/1     Running   0
kube-apiserver-k8s-master           1/1     Running   0
kube-controller-manager-k8s-master  1/1     Running   0
kube-scheduler-k8s-master           1/1     Running   0
kube-proxy-xxxxx                    1/1     Running   0
kube-flannel-ds-xxxxx               1/1     Running   0
```

All components should be in **Running** state.

---

# 🧪 Step 5 — Verify Pod Scheduling Across Nodes

You can confirm Kubernetes is able to schedule workloads across nodes.

Run:

```bash
kubectl get pods -o wide
```

This command displays:

- Pod IP address
- Node hosting the pod
- Pod status

Example output:

```text
NAME        READY   STATUS    NODE
nginx-pod   1/1     Running   k8s-worker2
```

This confirms the cluster can run workloads on the new worker node.

---

# ✅ Result

You have successfully expanded your Kubernetes cluster.

You learned how to:

- Generate a secure `kubeadm join` command
- Join a new worker node to the cluster
- Verify node registration
- Validate cluster health after scaling

Your Kubernetes cluster is now **horizontally scalable** by simply adding more worker nodes when needed.

---

| [« Lab 02: Networking](../02-kubernetes-networking-cni/README.md) | [Main Directory](../../README.md) | [Lab 04: Kubernetes Services »](../04-kubernetes-services/README.md) |
| :--- | :---: | ---: |
