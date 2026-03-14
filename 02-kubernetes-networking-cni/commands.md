# 💻 Lab 02: Commands for Kubernetes Networking (CNI)

This document contains all the commands used to deploy, validate, and troubleshoot networking and CNI functionality within the cluster.

---

## 🔍 1. Initial Cluster Verification
Before installing the CNI, check the status of your nodes and system pods.

```bash
# Check node status (Expect: NotReady)
kubectl get nodes

# Check system pods (Expect: CoreDNS pods in Pending state)
kubectl get pods -n kube-system
```

---

## 🌐 2. Deploy Flannel CNI
Apply the Flannel network manifest to enable Pod-to-Pod communication.

```bash
# Apply Flannel configuration
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml

# Verify Flannel pods are running
kubectl get pods -n kube-flannel

# Check Flannel pods with node placement detail
kubectl get pods -n kube-flannel -o wide
```

---

## 🚀 3. Testing Pod-to-Pod Communication
We will create two pods on different nodes to verify that the network fabric is working.

### Create Test Pods
```bash
# Create an NGINX pod (Pod A)
kubectl run pod-a --image=nginx --restart=Never

# Create a BusyBox pod for testing (Pod B)
kubectl run pod-b --image=busybox --restart=Never -- sleep 3600
```

### Identify Pod Networking Details
```bash
# Check Pod IPs and which nodes they are running on
kubectl get pods -o wide
```

### Execute Ping Test
```bash
# Get Pod-A IP address
kubectl get pod pod-a -o jsonpath='{.status.podIP}'

# Exec into Pod-B to ping Pod-A
kubectl exec -it pod-b -- ping <PASTE-POD-A-IP-HERE>

# Exit the pod session
exit
```

---

## 🛠️ 4. Troubleshooting Commands
Use these commands if your pods are not communicating or nodes remain `NotReady`.

### Cluster-Wide Overview
```bash
# View pods across all namespaces
kubectl get pods -A

# View detailed pod placement and IP addresses
kubectl get pods -o wide
```

### Deep Dive Inspection
```bash
# Describe a pod to view lifecycle events and errors
kubectl describe pod <pod-name>

# Check logs of the CNI pods for networking errors
kubectl logs -n kube-flannel <flannel-pod-name>
```

### Network Interface Verification (On Nodes)
```bash
# Run on Master or Worker node to see the flannel interface
ip a | grep flannel
```

---

> [!TIP]
> **What's Next?**
> Now that your network is stable and pods can talk to each other, proceed to **[Lab 03: Deploying Your First Application](../03-deploy-first-app/README.md)**.
