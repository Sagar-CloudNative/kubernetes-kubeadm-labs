# Commands — Lab 17: Kubernetes RBAC Advanced Real-World Scenarios

---

## 🟢 Initial Setup

```bash
kubectl create ns dev
kubectl create ns qa
kubectl create ns prod

kubectl create deployment nginx --image=nginx:1.27 -n dev
kubectl create deployment nginx --image=nginx:1.27 -n qa
kubectl create deployment nginx --image=nginx:1.27 -n prod

# Verify
kubectl get pods -n dev
kubectl get pods -n qa
kubectl get pods -n prod
```

---

## 🎯 Scenario 1 — Accidental Over-Permission (Misconfiguration)

### Create the wrong binding
```bash
kubectl create clusterrolebinding temp-debug-access \
  --clusterrole=view \
  --serviceaccount=dev:default
```

### Verify binding is active
```bash
kubectl get clusterrolebinding temp-debug-access
```

### Test — cross-namespace access (should NOT work but does)
```bash
kubectl get pods -n prod --as=system:serviceaccount:dev:default
kubectl get pods -n qa --as=system:serviceaccount:dev:default
```

### Limitation check — secrets are still blocked
```bash
kubectl get secrets -n prod --as=system:serviceaccount:dev:default
```

### Fix — remove the misconfigured binding
```bash
kubectl delete clusterrolebinding temp-debug-access
```

### Verify fix
```bash
kubectl get clusterrolebinding temp-debug-access
```

### Re-test — access should be blocked now
```bash
kubectl get pods -n prod --as=system:serviceaccount:dev:default
```

---

## 🎯 Scenario 2 — RBAC Design at Scale (Reusable Roles)

### Create one reusable ClusterRole
```bash
kubectl create clusterrole read-only-access \
  --verb=get,list,watch \
  --resource=pods
```

### Verify the role
```bash
kubectl describe clusterrole read-only-access
kubectl get clusterrole read-only-access
```

### Bind to QA team
```bash
kubectl create rolebinding qa-binding \
  --clusterrole=read-only-access \
  --serviceaccount=qa:default \
  -n qa
```

### Bind to Dev team
```bash
kubectl create rolebinding dev-binding \
  --clusterrole=read-only-access \
  --serviceaccount=dev:default \
  -n dev
```

### Validate — Dev accesses its own namespace (Allowed)
```bash
kubectl get pods -n dev --as=system:serviceaccount:dev:default
```

### Validate — QA accesses its own namespace (Allowed)
```bash
kubectl get pods -n qa --as=system:serviceaccount:qa:default
```

### Validate — Dev tries QA namespace (Denied)
```bash
kubectl get pods -n qa --as=system:serviceaccount:dev:default
```

---

## 🎯 Scenario 3 — What RBAC Does NOT Protect

### Find a running pod
```bash
kubectl get pods -n dev
```

### Exec into the container
```bash
kubectl exec -it <pod-name> -n dev -- sh
```

### Inside the container — RBAC has no control here
```bash
ls
whoami
```

### Exit the container
```bash
exit
```

---

## 🎯 Scenario 4 — Real Debugging Workflow

### Trigger a Forbidden error
```bash
kubectl get secrets -n prod --as=system:serviceaccount:dev:default
```

### Debug — check permission using auth can-i
```bash
kubectl auth can-i get secrets --as=system:serviceaccount:dev:default -n prod
```

### Inspect the role — find what's allowed
```bash
kubectl describe clusterrole read-only-access
```

---

## 🧹 Cleanup

```bash
kubectl delete rolebinding dev-binding -n dev
kubectl delete rolebinding qa-binding -n qa
kubectl delete clusterrole read-only-access
kubectl delete deployment nginx -n dev
kubectl delete deployment nginx -n qa
kubectl delete deployment nginx -n prod
kubectl delete ns dev qa prod

# Verify
kubectl get ns | grep -E 'dev|qa|prod'
```
