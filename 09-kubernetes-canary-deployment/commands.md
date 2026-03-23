# 🧪 Canary Deployment Lab (Step-by-Step)

---

## ⚙️ Pre-requisite

Pull the image before starting:

```bash
sudo ctr -n k8s.io images pull docker.io/hashicorp/http-echo:latest
```

---

## 🟦 Step 1 — Deploy v1 (Stable Version)

📄 File: `v1-deployment.yaml`

▶️ Apply

```bash
kubectl apply -f v1-deployment.yaml
```

✅ Verify

```bash
kubectl get deploy myapp-v1
kubectl get pods -l app=myapp
```

---

## 🟦 Step 2 — Create Service

📄 File: `service.yaml`

▶️ Apply

```bash
kubectl apply -f service.yaml
```

🌐 Test

```bash
kubectl get nodes -o wide
curl http://<NODE-IP>:30007
```

Expected output:

```
Hello from V1
```

---

## 🟦 Step 3 — Deploy v2 (Canary Version)

📄 File: `v2-deployment.yaml`

▶️ Apply

```bash
kubectl apply -f v2-deployment.yaml
```

✅ Verify

```bash
kubectl get deploy
kubectl get pods -l app=myapp
```

---

## 🟦 Step 4 — How Traffic Works

⚠️ Important:

- Kubernetes does NOT split traffic by percentage  
- Traffic is distributed randomly across pods  
- Traffic depends on number of replicas  

Example:

- v1 → 9 pods  
- v2 → 1 pod  

👉 Approx result: 90% / 10%

---

## 🟦 Step 5 — Verify Traffic

```bash
while true; do curl -s http://<NODE-IP>:30007; echo; sleep 1; done
```

Example output:

```
Hello from V1
Hello from V1
Hello from V2
Hello from V1
```

---

## 🟦 Step 6 — Increase Canary Traffic

```bash
kubectl scale deployment myapp-v2 --replicas=5
kubectl get deploy
```

🔍 Test Again

```bash
while true; do curl -s http://<NODE-IP>:30007; echo; sleep 1; done
```

👉 You will start seeing more responses from v2

⚠️ Note:

- Traffic is still approximate  
- Works better with higher request volume  

---

## 🟦 Step 7 — Full Rollout

```bash
kubectl scale deployment myapp-v2 --replicas=10
kubectl get deploy

kubectl scale deployment myapp-v1 --replicas=0
kubectl get deploy
```

👉 All traffic goes to v2

---

## 🟦 Step 8 — Rollback Scenario

```bash
kubectl scale deployment myapp-v1 --replicas=9
kubectl get deploy

kubectl scale deployment myapp-v2 --replicas=0
kubectl get deploy
```

🔍 Verify

```bash
while true; do curl -s http://<NODE-IP>:30007; echo; sleep 1; done
```

👉 All responses from v1

---

## 🧹 Cleanup

```bash
kubectl delete -f v1-deployment.yaml
kubectl delete -f v2-deployment.yaml
kubectl delete -f service.yaml
```

---

## 🧠 Key Takeaways

✔ Canary deployment releases to a small group first  
✔ Traffic increases gradually  
✔ Replica-based split is simple but approximate  
✔ Rollback is controlled and safe  
