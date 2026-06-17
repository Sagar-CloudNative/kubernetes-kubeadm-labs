# Commands — Lab 16: Namespace Governance & Cluster Control

---

## 🎯 Demo 1 — ResourceQuota (Hard Limit)

### Step 1 — Create Namespace
```bash
kubectl create namespace team-alpha
kubectl get ns
```

### Step 2 — Apply ResourceQuota
```bash
kubectl apply -f resourcequota.yaml
kubectl describe quota -n team-alpha
```

### Step 3 — Deploy Within Limit (Success)
```bash
kubectl apply -f deploy-ok.yaml
kubectl get pods -n team-alpha
```

### Step 4 — Exceed Quota (Partial Block)
```bash
kubectl apply -f deploy-fail.yaml

# Check pods — only some will be running
kubectl get pods -n team-alpha

# Check deployment replicas
kubectl get deploy big-app -n team-alpha

# Check deployment details
kubectl describe deploy big-app -n team-alpha

# Find the ReplicaSet name
kubectl get rs -n team-alpha

# Check root cause
kubectl describe rs <big-app-rs-name> -n team-alpha

# Verify quota usage
kubectl describe quota -n team-alpha
```

### Step 4.1 — Hard Failure (Complete Block)
```bash
# Clean previous deployment
kubectl delete deployment big-app -n team-alpha

# Fill the quota completely
kubectl scale deployment small-app --replicas=4 -n team-alpha
kubectl rollout status deployment small-app -n team-alpha
kubectl get pods -n team-alpha

# Verify quota is fully used
kubectl describe quota -n team-alpha

# Try to deploy again — should be fully blocked
kubectl apply -f deploy-fail.yaml
kubectl get pods -n team-alpha

# Check events for the exact error
kubectl get events -n team-alpha --sort-by=.metadata.creationTimestamp

# Optional deep verification
kubectl get deploy big-app -n team-alpha
kubectl describe deployment big-app -n team-alpha
```

---

## 🎯 Demo 2 — LimitRange (Default Injection)

### Step 5 — Create Namespace
```bash
kubectl create namespace team-beta
kubectl get ns
```

### Step 6 — Apply LimitRange
```bash
kubectl apply -f limitrange.yaml
kubectl describe limitrange -n team-beta
```

### Step 7 — Deploy Pod WITHOUT Resources
```bash
kubectl apply -f no-resource.yaml
kubectl get pod no-resource-pod -n team-beta
```

### Step 8 — Verify Auto-Injection (Wow Moment)
```bash
# Show full pod YAML — look for the annotation and resources section
kubectl get pod no-resource-pod -n team-beta -o yaml

# Optional — same result via describe
kubectl describe pod no-resource-pod -n team-beta
```

---

## 🎯 Demo 3 — Object Count Quota

### Step 9 — Apply Object Quota
```bash
kubectl apply -f object-quota.yaml
kubectl describe quota -n team-beta
```

### Step 10 — Create First LoadBalancer (Success)
```bash
kubectl create deployment nginx --image=nginx:1.27 -n team-beta
kubectl expose deployment nginx --type=LoadBalancer --port=80 -n team-beta
```

### Step 11 — Try Second LoadBalancer (Blocked)
```bash
kubectl create deployment nginx2 --image=nginx -n team-beta
kubectl expose deployment nginx2 --type=LoadBalancer --port=80 -n team-beta

# Verify quota is full
kubectl describe quota -n team-beta
```

---

## 🧹 Cleanup

```bash
kubectl delete namespace team-alpha
kubectl delete namespace team-beta
kubectl get ns
```
