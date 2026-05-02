# 💻 Lab 14: HPA Practical Commands

### 1️⃣ Install Metrics Server (Kubeadm Setup)
```bash
# Apply official manifests
kubectl apply -f [https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml](https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml)

# Patch for Lab Environments (Insecure TLS)
# Add '- --kubelet-insecure-tls' to container args
kubectl edit deployment metrics-server -n kube-system

# Verify Metrics are being collected (wait 1 min)
kubectl top nodes
```

### 2️⃣ Deploy Demo App & HPA
```bash
# Deploy Nginx with CPU requests
kubectl apply -f hpa-demo-deployment.yaml

# Expose as a service
kubectl expose deployment hpa-demo --port=80 --type=ClusterIP

# Create HPA (Target 50% CPU, Min 2, Max 10)
kubectl autoscale deployment hpa-demo --cpu-percent=50 --min=2 --max=10
```

### 3️⃣ Monitor & Load Test
```bash
# Terminal 1: Watch HPA status
kubectl get hpa -w

# Terminal 2: Watch Pods scaling
kubectl get pods -w

# Terminal 3: Generate Load
kubectl run -i --tty load-generator --image=busybox:latest -- /bin/sh
# Inside the pod, run:
while true; do wget -q -O- http://hpa-demo; done
```

### 4️⃣ Manual CPU Stress (If Nginx is too light)
```bash
# Update deployment to run a CPU-intensive command
kubectl edit deployment hpa-demo
# Add to container spec:
# command: ["/bin/sh"]
# args: ["-c", "while true; do yes > /dev/null; done"]
```

### 🧹 Cleanup
```bash
kubectl delete hpa hpa-demo
kubectl delete deployment hpa-demo
kubectl delete svc hpa-demo
kubectl delete pod load-generator
```
