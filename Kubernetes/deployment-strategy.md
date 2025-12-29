
## 🚀 Kubernetes Deployment Strategies

![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2ALgQh-4fxXIIb-BIsN-H0OQ.gif)


> **Note:**
> Using **Deployment / StatefulSet**, Kubernetes supports
> **scaling, load balancing, and rolling updates**.

---

## Deployment Strategies in Kubernetes

1. **Recreate strategy (Free strategy)**
2. **Rolling Update (default)**
3. **Blue–Green deployment**
4. **Canary deployment**

---

## 🔁 Rolling Update (Default Strategy)

### What is Rolling Update?

* Rolling Update is the **default strategy** in Kubernetes.
* Old Pods are replaced **one by one** with new Pods.
* **No downtime** (or very minimal).

---

### Example: Nginx Deployment (v1.22)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    type: proxy
spec:
  replicas: 1
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
          image: nginx:1.22
          ports:
            - containerPort: 80
              hostPort: 8080
```

```bash
kubectl apply -f deployment1.yml
```

👉 This creates an nginx Pod with **version 1.22**.

---

### Perform Rolling Update (Command Line)

```bash
kubectl set image deployment/nginx-deployment mynginx=nginx:1.23
```

* Updates image from **nginx:1.22 → nginx:1.23**
* Kubernetes creates new Pod with new version
* Old Pod is deleted **after new Pod is ready**

---

### Verify Update

```bash
kubectl describe pod <pod-name> | grep 1.23
```

👉 Confirms the new version is running.

---

### Key Point

* Rolling Update is **default** in Deployment.
* Used mostly in **production environments**.

---

## ♻️ Recreate Strategy (Free Strategy)

### What is Recreate Strategy?

* Kubernetes **deletes all old Pods first**
* Then creates new Pods with latest version
* **Downtime will occur**

---

### Recreate Strategy Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    type: proxy
spec:
  replicas: 2
  strategy:
    type: Recreate
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
          image: nginx:1.22
          ports:
            - containerPort: 80
              hostPort: 8080
```

---

### How Recreate Strategy Works

1. Old Pods are **deleted**
2. New Pods are **created**
3. Application is **down for some time**

---

### Where Recreate Strategy is Used?

* **Pre-prod / Dev environments**
* Cost-effective
* When downtime is acceptable

---

## 🧠 Easy Analogy (Interview Gold ⭐)

* **Rolling Update** = Changing tyres while car is moving 🚗
* **Recreate Strategy** = Stop car → change tyres → start again 🛑

---

## 🔑 One-Line Summary

* **Rolling Update** → zero/minimum downtime (default)
* **Recreate Strategy** → downtime, but simple and cheap

---

## ⭐ Interview Tip

Say this confidently:

> “Rolling update is the default Kubernetes deployment strategy that updates Pods gradually without downtime, while Recreate strategy deletes all old Pods first and causes downtime, mostly used in non-production environments.”





## 🔵🟢 Blue-Green Deployment & 🐦 Canary Deployment

![Image](https://learn.microsoft.com/en-us/azure/architecture/guide/aks/media/blue-green-aks-deployment-diagram-public-architecture.png)

![Image](https://miro.medium.com/0%2AM41gQ7P3kbXVr2h1)


## 🔵🟢 Blue-Green Deployment

### What is Blue-Green deployment?

* Suppose there are **100 servers** running **nginx:1.22**
  👉 This is called **Green deployment**
* Now you create a **new version nginx:1.23**
  👉 This is called **Blue deployment**
* Initially:

  * **NodePort / LoadBalancer** points to **nginx:1.22**
* Then:

  * You switch Service to point to **nginx:1.23**
* After testing:

  * Remove **nginx:1.22**
  * **nginx:1.23 becomes Green**

👉 Zero downtime, but **cost is high** (two environments).

---

## 🟢 Green Deployment (nginx:1.22)

### green.yml

```yaml
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
      version: "1.22"
  template:
    metadata:
      labels:
        type: proxy
        version: "1.22"
    spec:
      containers:
        - name: mynginx
          image: nginx:1.22
          ports:
            - containerPort: 80
              hostPort: 8080
```

```bash
kubectl apply -f green.yml
```

---

### Service for Green

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
  selector:
    type: proxy
    version: "1.22"
```

```bash
kubectl apply -f service1.yml
```

👉 This is **Green environment**.

---

## 🔵 Blue Deployment (nginx:1.23)

### blue.yml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: blue-nginx-deployment
  labels:
    type: proxy
spec:
  replicas: 2
  selector:
    matchLabels:
      type: proxy
      version: "1.23"
  template:
    metadata:
      labels:
        type: proxy
        version: "1.23"
    spec:
      containers:
        - name: mynginx
          image: nginx:1.23
          ports:
            - containerPort: 80
              hostPort: 8080
```

---

### Service for Blue

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30008
  selector:
    type: proxy
    version: "1.23"
```

```bash
kubectl apply -f blue.yml
kubectl apply -f serviceblue.yml
```

👉 Now traffic moves to **nginx:1.23**
👉 Old **nginx:1.22** is removed
👉 **Blue becomes Green**

---

### Blue-Green Summary

* ✔ Zero downtime
* ✔ Easy rollback
* ❌ Costly (two environments)
* ✔ Used by many companies

---

## 🐦 Canary Deployment

### What is Canary deployment?

* Suppose **100 Pods** running **nginx:1.22**
* You deploy **only 5 Pods** with **nginx:1.23**
* Small traffic goes to new version
* If no issues:

  * Increase nginx:1.23 Pods
  * Remove nginx:1.22 Pods

👉 Safer than Blue-Green
👉 Uses **partial traffic**

---

### Canary Example (Concept)

* 95 Pods → nginx:1.22
* 5 Pods → nginx:1.23

Gradually:

* 80 / 20
* 50 / 50
* 100% new version

---

## 🧠 Easy Analogy (Interview Gold ⭐)

* **Blue-Green** = Two houses 🏠🏠

  * Shift everyone at once

* **Canary** = Food testing 🍲

  * First give to few people
  * If safe → serve everyone

---

## 🔑 One-Line Summary

* **Blue-Green** → switch traffic fully to new version
* **Canary** → slowly test new version with few users

---

## ⭐ Interview Tip

Say this clearly:

> “Blue-Green deployment uses two identical environments for zero downtime releases, while Canary deployment gradually shifts traffic to the new version to reduce risk.”

