# 🚀 Kubernetes Cluster Initialization (kubeadm)

This section covers the steps required to initialize the **Kubernetes control plane**, install networking, and join the worker node to the cluster.

---

## 👑 Phase 7: Initialize Control Plane (Master Node)

Run this command on the **master node only**.

```bash
sudo kubeadm init \
--apiserver-advertise-address=192.168.56.115 \
--pod-network-cidr=10.244.0.0/16
```

**This command performs several tasks:**
* Generates Kubernetes certificates
* Starts control plane components (API Server, Scheduler, etc.)
* Creates the admin kubeconfig file
* Generates the `kubeadm join` command for worker nodes

> [!IMPORTANT]
> At the end of the output, you will see a command starting with `kubeadm join`. **Copy and save this command.** You will need it for the worker node later.

---

## 🧑‍💻 Phase 8: Configure kubectl Access
After initialization, configure `kubectl` so your current user can manage the cluster.

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

**Verify cluster access:**
```bash
kubectl get nodes
```
*Note: The master node will show as `NotReady` until the network plugin is installed in the next step.*

---

## 🌐 Phase 9: Install Pod Network (Flannel)
Kubernetes requires a CNI (Container Network Interface) plugin for Pod-to-Pod communication.

**Install Flannel:**
```bash
kubectl apply -f [https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml](https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml)
```

**Verify the network pods:**
```bash
kubectl get pods -n kube-system
```
*After a minute, run `kubectl get nodes` again. The master should now be `Ready`.*

---

## 👷 Phase 10: Join Worker Node
Switch to the **worker node** and run the join command you copied during Phase 7.

**Example:**
```bash
sudo kubeadm join 192.168.56.115:6443 \
--token <your-token> \
--discovery-token-ca-cert-hash sha256:<your-hash>
```

---

## ✅ Phase 11: Verify Cluster
Return to the **master node** to verify that the worker has joined and is healthy.

```bash
kubectl get nodes
```

**Expected Output:**
```text
NAME         STATUS   ROLES           AGE   VERSION
k8s-master   Ready    control-plane   10m   v1.29.x
k8s-worker   Ready    <none>          5m    v1.29.x
```

---

## 🎉 Result
You have successfully:
1. Initialized a Kubernetes control plane.
2. Configured local `kubectl` access.
3. Installed CNI (Flannel) networking.
4. Joined a worker node to the cluster.

**The cluster is now ready for deploying workloads!**
