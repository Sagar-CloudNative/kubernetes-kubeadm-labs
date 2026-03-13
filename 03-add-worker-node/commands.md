# ⚙️ Commands Used in Lab 03

This document lists all commands used to **add a new worker node to an existing Kubernetes cluster and demonstrate horizontal scaling**.

---

# 1️⃣ Check Current Cluster Nodes

Run on the **control plane node**.

```bash
kubectl get nodes -o wide
```

Example output:

```text
NAME          STATUS   ROLES           AGE
k8s-master    Ready    control-plane   1h
k8s-worker    Ready    <none>          55m
```

---

# 2️⃣ Inspect Worker Node Capacity

```bash
kubectl describe node k8s-worker
```

Scroll to:

- Capacity
- Allocated resources

This helps understand how much CPU and memory the node can handle.

---

# 3️⃣ Create Workload Pressure

Create a deployment with multiple pods.

```bash
kubectl create deployment stress-app --image=nginx --replicas=8
```

Verify pods:

```bash
kubectl get pods -o wide
```

You will notice all pods are scheduled on the same worker node.

---

# 4️⃣ Prepare New Worker Node

Run these commands on the **new worker node**.

Set hostname:

```bash
sudo hostnamectl set-hostname k8s-worker2
```

Refresh terminal:

```bash
exec bash
```

---

# 5️⃣ Update Hosts File

Edit:

```bash
sudo vi /etc/hosts
```

Add cluster nodes:

```text
192.168.56.115   k8s-master
192.168.56.116   k8s-worker
192.168.56.117   k8s-worker2
```

---

# 6️⃣ Test Network Connectivity

From master:

```bash
ping <worker-ip>
```

From worker:

```bash
ping <master-ip>
```

---

# 7️⃣ Update System Packages

```bash
sudo dnf update -y
```

---

# 8️⃣ Disable Swap

Check swap:

```bash
free -h
```

Disable swap:

```bash
sudo swapoff -a
```

Remove swap entry:

```bash
sudo sed -i '/swap/d' /etc/fstab
```

Verify:

```bash
cat /etc/fstab
```

---

# 9️⃣ Disable Firewall (Lab Environment)

```bash
sudo systemctl disable firewalld --now
```

Verify:

```bash
sudo systemctl status firewalld
```

---

# 🔟 Enable Required Kernel Modules

Load modules:

```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

Make modules persistent:

```bash
echo "overlay" | sudo tee /etc/modules-load.d/overlay.conf
echo "br_netfilter" | sudo tee /etc/modules-load.d/br_netfilter.conf
```

---

# 1️⃣1️⃣ Configure Kubernetes Networking

Create sysctl configuration:

```bash
sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```

Apply configuration:

```bash
sudo sysctl --system
```

Verify settings:

```bash
sysctl net.bridge.bridge-nf-call-iptables
sysctl net.bridge.bridge-nf-call-ip6tables
sysctl net.ipv4.ip_forward
```

Expected output:

```text
1
1
1
```

---

# 1️⃣2️⃣ Install Time Synchronization

```bash
sudo dnf install -y chrony
```

Enable service:

```bash
sudo systemctl enable chronyd --now
```

---

# 1️⃣3️⃣ Install Container Runtime (containerd)

Add repository:

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

Update cache:

```bash
sudo dnf makecache
```

Install containerd:

```bash
sudo dnf install -y containerd
```

Generate config:

```bash
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```

Edit config:

```bash
sudo vi /etc/containerd/config.toml
```

Set:

```
SystemdCgroup = true
```

Start containerd:

```bash
sudo systemctl enable --now containerd
```

Verify:

```bash
sudo systemctl status containerd
```

---

# 1️⃣4️⃣ Install Kubernetes Components

Add Kubernetes repo:

```bash
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
EOF
```

Install tools:

```bash
sudo dnf install -y kubelet kubeadm kubectl
```

Enable kubelet:

```bash
sudo systemctl enable --now kubelet
```

---

# 1️⃣5️⃣ Generate Join Command

Run on **control plane node**.

```bash
kubeadm token create --print-join-command
```

Example output:

```text
kubeadm join 192.168.56.115:6443 \
--token abcdef.1234567890abcdef \
--discovery-token-ca-cert-hash sha256:xxxxxxxx
```

---

# 1️⃣6️⃣ Join Worker Node

Run on **new worker node**.

```bash
sudo kubeadm join 192.168.56.115:6443 \
--token abcdef.1234567890abcdef \
--discovery-token-ca-cert-hash sha256:xxxxxxxx
```

---

# 1️⃣7️⃣ Verify Node Joined

Run on control plane.

```bash
kubectl get nodes
```

Expected output:

```text
NAME            STATUS   ROLES
k8s-master      Ready    control-plane
k8s-worker      Ready    <none>
k8s-worker2     Ready    <none>
```

---

# 1️⃣8️⃣ Label Worker Node (Optional)

```bash
kubectl label node k8s-worker2 node-role.kubernetes.io/worker=worker
```

---

# 1️⃣9️⃣ Scale Application

Increase replicas:

```bash
kubectl scale deployment stress-app --replicas=15
```

Verify pods:

```bash
kubectl get pods -o wide
```

Pods will now be scheduled across **multiple worker nodes**.

---

# ✅ Result

You successfully expanded the Kubernetes cluster by adding a new worker node.

This demonstrates **horizontal scaling**, where cluster capacity increases by adding more nodes instead of upgrading a single machine.
