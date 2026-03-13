# Kubernetes Cluster Setup Commands

This file contains all commands used in the **Lab 01 – 2-Node Kubernetes Cluster Setup using kubeadm**.

---

## 1. Set Hostnames

Run on **Master Node**:
```bash
sudo hostnamectl set-hostname k8s-master
exec bash
```

Run on **Worker Node**:
```bash
sudo hostnamectl set-hostname k8s-worker
exec bash
```

## 2. Update /etc/hosts
Run on **both nodes**:
```bash
sudo vi /etc/hosts
```

Example configuration:
```text
192.168.56.115   k8s-master
192.168.56.116   k8s-worker
```

## 3. Check IP Address
```bash
ip a
```

## 4. Test Network Connectivity
From **master node**:
```bash
ping <worker-ip>
```

From **worker node**:
```bash
ping <master-ip>
```

## 5. Test SSH Connectivity
From **master**:
```bash
ssh user@<worker-ip>
```

## 6. Update System Packages
Run on **both nodes**:
```bash
sudo dnf update -y
```

## 7. Disable Swap
Check swap:
```bash
free -h
```

Disable swap:
```bash
sudo swapoff -a
```

Make it permanent:
```bash
sudo vi /etc/fstab
```
*(Comment out the swap line)*

Verify:
```bash
free -h
```

## 8. Disable Firewall (Lab Environment)
Check firewall status:
```bash
sudo systemctl status firewalld
```

Disable firewall:
```bash
sudo systemctl disable firewalld --now
```

Verify:
```bash
sudo systemctl status firewalld
```

## 9. Enable Required Kernel Modules
Load modules:
```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

Make them persistent:
```bash
echo "overlay" | sudo tee /etc/modules-load.d/overlay.conf
echo "br_netfilter" | sudo tee /etc/modules-load.d/br_netfilter.conf
```

Verify:
```bash
cat /etc/modules-load.d/overlay.conf
cat /etc/modules-load.d/br_netfilter.conf
```

## 10. Configure Kernel Networking
Create configuration:
```bash
sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```

Apply settings:
```bash
sudo sysctl --system
```

Verify:
```bash
sysctl net.bridge.bridge-nf-call-iptables
sysctl net.bridge.bridge-nf-call-ip6tables
sysctl net.ipv4.ip_forward
```

## 11. Install Time Synchronization (chrony)
```bash
sudo dnf install -y chrony
sudo systemctl enable chronyd --now
```

## 12. Install Container Runtime (containerd)
Install repository tools:
```bash
sudo dnf install -y dnf-plugins-core
```

Add Docker repository:
```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

Install containerd:
```bash
sudo dnf install -y containerd.io
```

Create default configuration and set SystemdCgroup:
```bash
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
```

Start containerd:
```bash
sudo systemctl enable --now containerd
```

Check status:
```bash
sudo systemctl status containerd
```

## 13. Install Kubernetes Components
Create Kubernetes repository:
```bash
sudo tee /etc/yum.repos.d/kubernetes.repo <<EOF
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
sudo systemctl enable kubelet
```
