# 💻 Lab 13: RBAC Commands

### 1️⃣ Setup Namespaces & Identity
```bash
# Create namespaces
kubectl create namespace dev
kubectl create namespace prod

# Create ServiceAccount in dev
kubectl create serviceaccount dev-sa -n dev
```

### 2️⃣ Implement Namespace-Level Access (Role)
```bash
# Apply Role and Binding
kubectl apply -f role.yaml
kubectl apply -f rolebinding.yaml

# Verify
kubectl get role,rolebinding -n dev
```

### 3️⃣ Test Permissions (The "can-i" power)
```bash
# Check if dev-sa can list pods in dev (Expected: yes)
kubectl auth can-i list pods --as=system:serviceaccount:dev:dev-sa -n dev

# Check if dev-sa can delete pods in dev (Expected: no)
kubectl auth can-i delete pods --as=system:serviceaccount:dev:dev-sa -n dev

# Check if dev-sa can list pods in prod (Expected: no)
kubectl auth can-i list pods --as=system:serviceaccount:dev:dev-sa -n prod
```

### 4️⃣ Implement Cluster-Level Access (ClusterRole)
```bash
# Apply ClusterRole and ClusterRoleBinding
kubectl apply -f clusterrole.yaml
kubectl apply -f clusterrolebinding.yaml

# Check if dev-sa can list nodes (Expected: yes)
kubectl auth can-i list nodes --as=system:serviceaccount:dev:dev-sa
```

### 🧹 Cleanup
```bash
kubectl delete ns dev prod
kubectl delete clusterrole node-reader
kubectl delete clusterrolebinding node-reader-binding
```
