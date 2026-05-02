# 🏭 Lab 12: Production Deployment Workflow for DevOps Teams

This lab moves beyond simple "kubectl" commands and explores the end-to-end industrial process of how modern DevOps teams ship code to production safely and reliably.

---

## 🎥 Video Tutorial

Watch the full breakdown of the Production Lifecycle:

👉 [**Watch the Deployment Workflow Video**](https://youtu.be/NkcliZ00jlQ?si=Lm-RB7cbMiRhANeo)

---

## 📘 The DevOps Lifecycle Blueprint



### 1. Code to Build Phase
* **Trigger:** Developer pushes code to a Git branch and creates a Pull Request (PR).
* **CI Pipeline:** Automatically triggers a webhook to run "Linting" (code style checks) and "Unit Tests."
* **Artifact:** If tests pass, a Docker image is built using a `Dockerfile`.

### 2. Image & Registry Phase
* **Tagging:** Never use `latest`. Use Semantic Versioning (e.g., `v1.0.1`) or Git Commit SHAs.
* **Security:** Images are scanned for vulnerabilities before being pushed to a private Container Registry (ECR, DockerHub, etc.).

### 3. Deployment Phase
Teams choose a strategy based on risk tolerance:
* **Rolling Update:** Default, replaces pods one-by-one.
* **Blue-Green:** Two identical environments, instant traffic switch.
* **Canary:** Small release to 5-10% of users first.

### 4. Monitoring & Validation
"The deployment isn't finished just because the pods are running." 
* **Liveness/Readiness Probes:** Ensure the app is actually responding.
* **Key Metrics:** Monitor Latency, Error Rates, and CPU/Memory Spikes.

### 5. The Rollback Mechanism
In production, **MTTR (Mean Time To Recovery)** is the most important metric.
* If metrics show an error spike, trigger a rollback immediately (e.g., `helm rollback`).
* **Rule:** Investigate the "Why" *after* the service is restored.

---

## ⭐ Production Best Practices (The Golden Rules)

| Rule | Why it matters |
| :--- | :--- |
| **No `latest` tags** | Prevents accidental deployments of untested code. |
| **Immutable Tags** | Ensures that the image running in Dev is exactly the same as Production. |
| **Health Checks** | Prevents Kubernetes from sending traffic to a crashing application. |
| **Automated Rollback** | Reduces downtime by removing human decision delays during a crisis. |
| **State Awareness** | Ensure session persistence and DB compatibility before switching versions. |

---

## 🎯 Key Takeaways
1. **Automation is King:** Manual deployments are the #1 cause of production outages.
2. **Visibility is Mandatory:** You cannot manage what you cannot measure (Logs + Metrics).
3. **Safety First:** Always have a "Plan B" (Rollback) ready before you execute "Plan A" (Deployment).

---

[⬅️ Previous](../11-deployment-strategies-comparison) | [🏠 Home](../README.md) | [Next Lab: RBAC ➡️](../13-kubernetes-rbac)
