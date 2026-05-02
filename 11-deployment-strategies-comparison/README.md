# ⚖️ Lab 11: Deployment Strategies Comparison

This lab provides a deep-dive comparison between the three most common Kubernetes deployment strategies: **Rolling Update**, **Blue-Green**, and **Canary**. Understanding when to use each is critical for maintaining high availability in production.

---

## 🎥 Video Tutorial

Watch the strategy comparison and decision guide:

👉 [**Watch the Comparison Video**](https://youtu.be/CJ2pAOAJSzg?si=ilzMeJMNBZp2IZ2y)

---

## 📊 Quick Comparison Table

| Feature | Rolling Update | Blue-Green | Canary |
| :--- | :--- | :--- | :--- |
| **Downtime** | Zero | Zero | Zero |
| **Rollback Speed** | Slow | **Instant** | Medium |
| **Infrastructure Cost** | **Low** | High (Double) | Medium |
| **Complexity** | Simple (Default) | Medium | High |
| **Risk Mitigation** | Medium | High | **Highest** |
| **Best For** | Routine Updates | Critical Apps | High Traffic/New Features |

---

## 📘 Deep Dive into Strategies

### 🔄 1. Rolling Update (The Default)
Kubernetes replaces pods one-by-one. 
* **Pros:** No extra cost, built-in, easy to use.
* **Cons:** During the update, two versions coexist. If the new version has a bug, it might affect users before the update is finished.

### 🔵🟢 2. Blue-Green (The Safest Rollback)
Two identical environments. Switch traffic from "Blue" to "Green" via a LoadBalancer or Service change.
* **Pros:** Instant rollback. If Green fails, switch back to Blue in seconds.
* **Cons:** Expensive. You need 2x the CPU and RAM to run both versions simultaneously.

### 🐤 3. Canary (The Real-World Test)
Release to a small group (10%) then scale up.
* **Pros:** Catch bugs with real user traffic without affecting everyone.
* **Cons:** Hard to achieve exact traffic splitting (e.g., 90/10) without a Service Mesh like Istio or an Ingress Controller.

---

## 🛠️ Decision Logic: Which one should you choose?

1. **Need Instant Rollback?** → Use **Blue-Green**.
2. **Want to test with real users first?** → Use **Canary**.
3. **Limited Budget/Resources?** → Use **Rolling Update**.
4. **Simple Microservice?** → Use **Rolling Update**.

---

## ⚠️ Common Pitfalls to Avoid

* **Database Compatibility:** Regardless of the strategy, your database changes **must** be backward compatible so the old version doesn't crash while the new version is being deployed.
* **Session Persistence:** If you use "Sticky Sessions," moving a user from V1 to V2 mid-transaction might log them out or break their cart.
* **Monitoring:** For Canary deployments, you **must** have a dashboard. You cannot "guess" if the 10% of users are having a good experience.

---

[⬅️ Previous](../10-kubernetes-canary-deployment) | [🏠 Home](../README.md) | [Next Lab: Production Workflow ➡️](../12-production-deployment-workflow)
