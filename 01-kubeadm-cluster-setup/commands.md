# Commands Used in This Lab

This document contains all commands used to build the **2-Node Kubernetes Cluster using kubeadm**.

---

## Set Hostnames

Run on **master node**

```bash
sudo hostnamectl set-hostname k8s-master
exec bash

Run on worker node

sudo hostnamectl set-hostname k8s-worker
exec bash
Update /etc/hosts

Run on both nodes

sudo vim /etc/hosts

Example:

192.168.56.115 k8s-master
192.168.56.116 k8s-worker
Check IP Address
ip a
Test Connectivity

From master:

ping <worker-ip>

From worker:

ping <master-ip>
Test SSH Access
ssh user@<worker-ip>
Update System

Run on both nodes

sudo dnf update -y
Disable Swap

Check swap:

free -h

Disable swap:

sudo swapoff -a

Make permanent:

sudo vim /etc/fstab

Comment the swap entry.

Disable Firewall (Lab Environment)
sudo systemctl disable firewalld --now

Verify:

sudo systemctl status firewalld
Enable Required Kernel Modules
sudo modprobe overlay
sudo modprobe br_netfilter

Make them persistent:

echo overlay | sudo tee /etc/modules-load.d/overlay.conf
echo br_netfilter | sudo tee /etc/modules-load.d/br_netfilter.conf
Configure Kernel Networking

Create config file:

sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

Apply settings:

sudo sysctl --system
Install Time Synchronization
sudo dnf install -y chrony
sudo systemctl enable chronyd --now
Install Container Runtime (containerd)

Install tools:

sudo dnf install -y dnf-plugins-core

Add repository:

sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

Install containerd:

sudo dnf install -y containerd.io

Create default config:

sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

Start container runtime:

sudo systemctl enable --now containerd

Verify:

sudo systemctl status containerd
Install Kubernetes Components

Create repo:

sudo tee /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
EOF

Install tools:

sudo dnf install -y kubelet kubeadm kubectl

Enable kubelet:

sudo systemctl enable kubelet

➡ Next: continue with kubeadm-init.md to initialize the cluster.


---

# How GitHub Will Display It

GitHub will show:


Command block
Command block
Command block


with **copy buttons**, which is perfect for tutorials.

---

# Small Tip (Important for DevOps Repos)

Use these block types:


bash → commands
yaml → kubernetes files
text → output


Example:

```yaml
apiVersion: v1
kind: Pod
