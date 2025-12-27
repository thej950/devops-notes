![Image](https://cdn.prod.website-files.com/681e366f54a6e3ce87159ca4/6877c48368669fc5b3f179f1_image6.png)

![Image](https://cdn.prod.website-files.com/633e9bad8f71dfa75ae4c9db/6357fcc4b3a1634d362a408a_CPU%20Limits.webp)

![Image](https://media.licdn.com/dms/image/v2/D4D12AQGx-gVVW8IhvA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1703492197142?e=2147483647\&t=kzOtIz-LJVevLAVeNQqviTQqGItR9FmCllzL438qKpQ\&v=beta)

## ⚖️ Requests and Limits in Kubernetes (Beginner Friendly)

### What are **Requests**?

* **Request** = **minimum resources** a Pod needs to run.
* Kubernetes uses requests to **schedule Pods on nodes**.
* If a node does not have enough requested CPU or memory, the Pod **will not be scheduled**.

👉 Think: *“At least this much I need.”*

---

### What are **Limits**?

* **Limit** = **maximum resources** a Pod can use.
* Pod **cannot exceed** this value.
* If it tries:

  * CPU → throttled
  * Memory → Pod is killed (OOMKilled)

👉 Think: *“Do not cross this line.”*

---

## 🔁 Simple Flow

1. Pod asks for resources (**request**).
2. Scheduler checks available node resources.
3. Pod is placed on a node.
4. Pod can use resources **up to limit**.

---

## 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Hotel Room Booking** 🏨
* **Request** = Minimum room size you book
* **Limit** = Maximum guests allowed in the room

👉 Below request → not allowed
👉 Above limit → kicked out

---

## 📄 Example (Pod)

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "64Mi"
  limits:
    cpu: "500m"
    memory: "128Mi"
```

### Meaning:

* Pod is guaranteed:

  * 250 millicore CPU
  * 64 MB memory
* Pod can use up to:

  * 500 millicore CPU
  * 128 MB memory

---

## 🧩 Pod vs Deployment

* Requests and limits work the **same way** for:

  * Pod
  * Deployment
  * StatefulSet

---

## ⚠️ Important Notes

* CPU is **compressible** → throttled if exceeded.
* Memory is **not compressible** → Pod killed if exceeded.
* Always define requests and limits in **production**.

---

## 🔑 One-Line Difference

* **Request** → minimum guaranteed resource
* **Limit** → maximum allowed resource

---

## ⭐ Interview Tip

Say this clearly:

> “Requests define the minimum resources required for scheduling a Pod, while limits define the maximum resources a Pod can consume.”


# Deployment Example 

```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    type: proxy
spec:
  replicas: 2
  selector:
    matchLabels:
      type: proxy
  template:
    metadata:
      name: nginx-pod
      labels:
        type: proxy
    spec:
      containers:
        - name: mynginx
          image: nginx
          resources:
            requests:
              cpu: "250m"
              memory: "64Mi"
            limits:
              cpu: "500m"
              memory: "128Mi"
              
```
