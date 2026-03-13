Kubernetes Cluster Setup Guide (2-Node)
This document outlines the step-by-step commands required to bootstrap a 2-node Kubernetes cluster using kubeadm, containerd, and dnf.

1. Node Preparation
Set Hostnames
Assign unique identities to each node for easier management.

On Master Node:

Bash
sudo hostnamectl set-hostname k8s-master
exec bash
On Worker Node:

Bash
sudo hostnamectl set-hostname k8s-worker
exec bash
Configure Local DNS
Update the /etc/hosts file on both nodes so they can communicate via hostname.

Bash
sudo vi /etc/hosts
Add the following (replace with your actual IPs):

Plaintext
192.168.56.115 k8s-master
192.168.56.116 k8s-worker
Network Connectivity Check
Ensure nodes can "see" each other.

Bash
# Check local IP
ip a

# Ping the opposite node
ping k8s-worker  # Run from master
ping k8s-master  # Run from worker
2. System Configuration
Run these commands on both nodes.

Disable Swap
Kubernetes requires swap to be disabled for the kubelet to function correctly.

Bash
# Disable swap immediately
sudo swapoff -a

# Disable swap permanently (Comment out the swap line)
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
Update System & Disable Firewall
In a production environment, you would open specific ports. For this lab, we will disable the firewall.

Bash
sudo dnf update -y
sudo systemctl disable firewalld --now
Kernel Modules & Networking
Configure the underlying Linux kernel to allow bridged traffic.

Bash
# Load modules
sudo modprobe overlay
sudo modprobe br_netfilter

# Persist modules on boot
sudo tee /etc/modules-load.d/k8s.conf <<EOF
overlay
br_netfilter
EOF

# Configure sysctl parameters
sudo tee /etc/sysctl.d/k8s.conf <<EOF
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply changes
sudo sysctl --system
3. Install Container Runtime (containerd)
Kubernetes requires a Container Runtime Interface (CRI). We will use containerd.

Bash
# Add Docker repository
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Install containerd
sudo dnf install -y containerd.io

# Generate default configuration
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml > /dev/null

# Set SystemdCgroup to true (Critical for K8s)
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml

# Start and enable containerd
sudo systemctl enable --now containerd
4. Install Kubernetes Tools
Install the core components: kubeadm, kubelet, and kubectl.

Add Kubernetes Repository
Bash
sudo tee /etc/yum.repos.d/kubernetes.repo <<EOF
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/repodata/repomd.xml.key
EOF
Install Packages
Bash
sudo dnf install -y kubelet kubeadm kubectl --disableexcludes=kubernetes

# Enable the kubelet service
sudo systemctl enable --now kubelet
