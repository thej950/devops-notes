![Image](https://kubernetes.io/images/docs/kubernetes-cluster-architecture.svg)

![Image](https://matthewpalmer.net/kubernetes-app-developer/articles/networking-overview.png)

![Image](https://miro.medium.com/1%2AkvU3t_01FqvojFO22BZsOg.png)

## 📦 What is a Pod in Kubernetes?  

### What is a Pod?

* A **Pod** is the **smallest unit** in Kubernetes.
* A Pod **runs one or more containers**.
* Containers inside a Pod **share**:

  * same IP address
  * same network
  * same storage (if attached)

👉 Kubernetes **does not run containers directly**.
👉 It always runs containers **inside Pods**.

---

### Why do we need a Pod?

* To group containers that **work together**.
* To manage containers as **one unit**.
* To allow containers to **communicate easily**.

---

### What happens when you create a Pod?

1. Kubernetes schedules the Pod on a Node.
2. Pod pulls the container image (nginx, mysql, jenkins).
3. Container starts running inside the Pod.

---

### 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Node** = House
* **Pod** = Room
* **Container** = Person

👉 A person lives inside a room,
👉 a room exists inside a house.

That’s how **Container → Pod → Node** works 🏠

---

## Basic Pod Commands (From Your Session)

```bash
kubectl get nodes              # see nodes
kubectl run webserver1 --image=nginx   # create pod
kubectl get pods               # list pods
kubectl describe pod webserver1 # pod details
kubectl delete pod webserver1  # delete pod
```

---

## Pod Using Definition (Manifest) File

### Pod YAML Structure

```yaml
apiVersion: v1
kind: Pod
metadata:
spec:
```

### Meaning:

* **apiVersion** → Kubernetes API library
* **kind** → Object type (Pod)
* **metadata** → Pod name, labels
* **spec** → What runs inside the Pod

---

### Example 1: Nginx Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: mynginx
    image: nginx
```

---

### Example 2: Postgres Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: postgres-pod
spec:
  containers:
  - name: mydb
    image: postgres
    env:
    - name: POSTGRES_PASSWORD
      value: thej
```

---

### Example 3: Jenkins Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: jenkins-pod
spec:
  containers:
  - name: myjenkins
    image: jenkins/jenkins
    ports:
    - containerPort: 8080
```

---

## Important Points to Remember

* Pods are **temporary** (not permanent).
* If a Pod dies, it is **not recreated automatically**.
* For real applications, use **Deployment**, not Pod.

---

## Delete Resources

```bash
kubectl delete --all pods
kubectl delete --all svc
kubectl delete --all deployments
```

---

## 🔑 One-Line Summary

* **Pod** = smallest deployable unit in Kubernetes that runs containers.

---

## ⭐ Interview Tip

Say this confidently:

> “A Pod is the smallest unit in Kubernetes that runs one or more containers with shared network and storage.”






![Image](https://matthewpalmer.net/kubernetes-app-developer/multi-container-pod-design.png)

![Image](https://cdn.prod.website-files.com/626a25d633b1b99aa0e1afa7/66cc2d974fa129b7d5ec5e3d_65887169ffa52b46403f0908_image2.jpeg)

## 📦 Multi-Container Pod in Kubernetes

### What is a Multi-Container Pod?

* A **Multi-Container Pod** is a Pod that runs **more than one container**.
* All containers in the Pod:

  * Share **same IP**
  * Share **same storage (volume)**
  * Start and stop **together**

👉 Containers work as a **team** inside one Pod.

---

### Why do we use Multi-Container Pods?

* When containers **depend on each other**
* When one container **supports** another container

Common use cases:

* Logging
* Monitoring
* File processing
* Proxy + app

---

## 🧠 Easy Analogy (Interview Gold ⭐)

* **Pod** = Food delivery bike 🛵
* **Main container** = Delivery person
* **Sidecar container** = Google Maps

👉 Both ride the **same bike**
👉 Maps supports delivery person

That is a **Multi-Container Pod**

---

## 🔁 Most Common Pattern: Sidecar Pattern

* One **main container** runs the app
* One **sidecar container** supports it (logs, sync, proxy)

---

## ✅ Real Example: Nginx + Log Collector

### Scenario:

* Nginx writes logs
* Another container reads and processes logs

---

### Multi-Container Pod YAML Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-multicontainer
spec:
  volumes:
    - name: log-volume
      emptyDir: {}
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: log-volume
          mountPath: /var/log/nginx

    - name: log-reader
      image: busybox
      command: ["/bin/sh","-c","tail -f /var/log/nginx/access.log"]
      volumeMounts:
        - name: log-volume
          mountPath: /var/log/nginx
```

---

### What is happening here?

* `nginx` container writes logs
* `log-reader` container reads logs
* Both containers:

  * Share the same volume
  * Run inside the same Pod

---

## 🧩 Another Real Example

* **Main container** → Application (Java / NodeJS)
* **Sidecar** → Fluentd (log forwarding)
* **Sidecar** → Envoy (proxy)

---

## ❌ When NOT to use Multi-Container Pod?

* If containers are **independent**
* If apps can scale separately
  👉 Use **separate Pods + Services**

---

## 🔑 One-Line Summary

* **Multi-Container Pod** = multiple containers working together in one Pod.

---

## ⭐ Interview Tip

Say this clearly:

> “A multi-container Pod runs multiple tightly coupled containers that share the same network and storage, commonly using the sidecar pattern.”
