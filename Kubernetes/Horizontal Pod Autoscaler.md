![Image](https://miro.medium.com/v2/resize%3Afit%3A1400/1%2A0wJBUCAWTLAe62PHmhoLOQ.gif)

![Image](https://www.apptio.com/wp-content/uploads/hpa-autoscaling.png)

![Image](https://k21academy.com/wp-content/uploads/2024/06/CPT2406212146-899x675-1.gif)

## 🔁 Horizontal Pod Autoscaler (HPA)

### What is HPA?

* **Horizontal Pod Autoscaler (HPA)** automatically **increases or decreases Pods**.
* It works based on **CPU, memory, or custom metrics**.
* HPA scales **Pods**, not nodes.

---

### Why do we use HPA?

* To handle **high traffic automatically**.
* To save cost when traffic is low.
* No manual scaling needed.

---

### How HPA Works (Simple Steps)

1. Users send traffic to the application.
2. CPU/Memory usage increases.
3. HPA detects high usage.
4. HPA increases Pod count.
5. When load reduces, Pods are scaled down.

---

### Simple Example

* Normal load → 2 Pods
* High traffic → HPA scales to 5 Pods
* Low traffic → HPA scales back to 2 Pods

---

### 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Restaurant** = Application
* **Waiters** = Pods
* **Customers** = Traffic

👉 More customers → hire more waiters
👉 Less customers → reduce waiters

That is **HPA** 🍽️

---

### HPA Example (YAML)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 6
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

---

### Important Points

* HPA works with **Deployment / StatefulSet**.
* Requires **metrics-server**.
* Does **not** scale nodes (Cluster Autoscaler does that).

---

### One-Line Difference

* **HPA** → scales Pods
* **Cluster Autoscaler** → scales Nodes

---

## ⭐ Interview Tip

Say this clearly:

> “Horizontal Pod Autoscaler automatically scales Pods based on CPU or memory usage to handle changing traffic.”


## Why we use HPA?

* To handle **traffic automatically**.
* No need to scale Pods manually.
* Saves **cost and effort**.

---

## How HPA Works (Easy Steps)

1. You create a **Deployment** and **Service**.
2. You set **CPU requests and limits**.
3. You create an **HPA rule**.
4. Kubernetes watches CPU usage.
5. If CPU > threshold → Pods scale up.
6. If CPU < threshold → Pods scale down.

---

## Example: PHP-Apache Autoscaling

### Deployment + Service (autoscaling.yml)

* Starts with **1 Pod**
* CPU request: **200m**
* CPU limit: **500m**
* Service exposes the Pod using **ClusterIP**

```bash
kubectl apply -f autoscaler.yml
```

---

## Create Horizontal Pod Autoscaler

```bash
kubectl autoscale deployment php-apache \
  --cpu-percent=50 \
  --min=1 \
  --max=10
```

### Meaning:

* If CPU usage > **50%**
* Pods increase automatically
* Maximum Pods = **10**

---

## Check HPA Status

```bash
kubectl get hpa
kubectl get hpa --watch
```

* Shows current CPU usage
* Shows desired vs current Pods

---

## How to Test Autoscaling? (Load Test)

### Generate Load using BusyBox

```bash
kubectl run -i --tty lg --rm \
--image=busybox --restart=Never \
-- /bin/sh -c \
"while sleep 0.01; do wget -q -O- http://php-apache; done"
```

* This sends **continuous traffic**
* CPU usage increases
* HPA scales Pods automatically

---

## Cleanup Commands

```bash
kubectl delete -f autoscaler.yml
kubectl delete hpa php-apache
kubectl delete pod lg
kubectl delete all --all
```

---

## 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Application** = Shop
* **Pods** = Employees
* **Customers** = Traffic

👉 More customers → hire more employees
👉 Less customers → reduce employees

That is **Horizontal Pod Autoscaler** 🏪

---

## 🔑 One-Line Difference

* **HPA** → scales **Pods**
* **Cluster Autoscaler** → scales **Nodes**

---

## ⭐ Interview Tip

Say this clearly:

> “Horizontal Pod Autoscaler automatically scales Pods based on CPU or memory usage to handle traffic without downtime.”
