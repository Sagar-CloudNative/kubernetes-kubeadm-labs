# 💻 Lab 03: Add a Worker Node to the Kubernetes Cluster

This document contains the commands used to join a new worker node to an existing Kubernetes cluster and verify that the node becomes part of the cluster.

---

## 🔍 1. Verify Existing Cluster Nodes
Before adding a new worker node, confirm the current cluster state from the control plane node.

```bash
# Check current nodes in the cluster
kubectl get nodes

# Verify system pods are running
kubectl get pods -n kube-system
```

---

## 🔑 2. Generate the Join Command
On the **control plane node**, generate a join command that worker nodes will use to join the cluster.

```bash
# Generate kubeadm join command
kubeadm token create --print-join-command
```

Example output:

```bash
kubeadm join 192.168.56.115:6443 \
--token abcdef.1234567890abcdef \
--discovery-token-ca-cert-hash sha256:xxxxxxxxxxxxxxxxxxxxxxxx
```

Copy this command.  
You will run it on the **worker node**.

---

## 🔗 3. Join the Worker Node
Run the join command on the new worker node.

```bash
sudo kubeadm join <MASTER-IP>:6443 \
--token <token> \
--discovery-token-ca-cert-hash sha256:<hash>
```

Once the command completes successfully, the worker node will join the cluster.

---

## ✅ 4. Verify Node Registration
Return to the control plane node and verify the new node has joined the cluster.

```bash
kubectl get nodes
```

Example output:

```bash
NAME          STATUS   ROLES           AGE
k8s-master    Ready    control-plane   30m
k8s-worker1   Ready    <none>          20m
k8s-worker2   Ready    <none>          5m
```

---

## 🛠️ 5. Troubleshooting Commands
Use these commands if the worker node does not appear or remains in a `NotReady` state.

```bash
# View all cluster pods
kubectl get pods -A

# View nodes with additional details
kubectl get nodes -o wide

# Check kubelet service on worker node
sudo systemctl status kubelet

# View kubelet logs
journalctl -u kubelet -f
```

---

> [!TIP]
> **What's Next?**  
> Now that your cluster has multiple nodes, the next step is to deploy applications and observe how Kubernetes schedules pods across worker nodes.
