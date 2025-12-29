# 🟦 Kubernetes Architecture (Master & Worker)

Kubernetes follows a **Master–Worker (Control Plane–Node)** architecture.

![Image](https://miro.medium.com/1%2A30JgJtH4ZEs0HkFQJdUKRw.jpeg)

---

## 🟩 MASTER NODE (Control Plane Components)

The **master node controls the cluster**.
It **does not run application containers**.

---

### 1️⃣ Container Runtime

* Software used to run containers (Docker, containerd, CRI-O).
* Kubernetes supports multiple runtimes.

🧠 **Analogy:**
Container runtime = **Engine of a car**.

---

### 2️⃣ kube-apiserver

* Main **entry point** of Kubernetes.
* All commands go through this component.
* It **validates requests** and talks to other components.

🧠 **Analogy:**
kube-apiserver = **Reception desk** of the company.

---

### 3️⃣ kube-scheduler

* Decides **where a Pod should run**.
* Checks CPU, memory, node health.
* Chooses the **best worker node**.

🧠 **Analogy:**
kube-scheduler = **Seat allocator** in a cinema.

---

### 4️⃣ Controller Manager

* Ensures **desired state = actual state**.
* If Pod crashes → creates a new Pod.
* Manages ReplicaSet, Node, Job controllers.

🧠 **Analogy:**
Controller Manager = **Auto-pilot system**.

---

### 5️⃣ etcd

* Key-value **database of the cluster**.
* Stores:

  * Nodes info
  * Pods status
  * Cluster configuration
* Single source of truth.

🧠 **Analogy:**
etcd = **Brain + memory** of Kubernetes.

---

## 🟦 WORKER NODE (Slave Components)

Worker nodes **run application Pods**.

---

### 1️⃣ Container Runtime

* Runs containers inside Pods.
* Same runtime as master (Docker / containerd).

🧠 **Analogy:**
Runtime = **Kitchen stove** where food is cooked.

---

### 2️⃣ kubelet

* Agent running on every worker node.
* Receives instructions from **kube-apiserver**.
* Creates Pods and containers.
* Reports status back to master.

🧠 **Analogy:**
kubelet = **Floor manager** following head-office orders.

---

### 3️⃣ kube-proxy

* Manages **networking rules**.
* Connects Pods with Services.
* Handles load balancing at node level.

🧠 **Analogy:**
kube-proxy = **Traffic police** directing vehicles.

---

## 🔁 How Everything Works (Flow – Easy)

1. User runs `kubectl apply`
2. Request goes to **kube-apiserver**
3. Data stored in **etcd**
4. **Scheduler** selects best node
5. **kubelet** creates Pod
6. **Container runtime** runs container
7. **kube-proxy** handles networking
8. **Controller Manager** monitors health

🧠 **Analogy:**
Like placing an **online food order**:

* Reception → Kitchen → Delivery → Feedback

---

## 🔑 One-Line Interview Summary

> “Kubernetes architecture follows a master-worker model where the control plane manages scheduling, state, and configuration, and worker nodes run application workloads.”

---

## ⭐ Interview Tip

Always say this confidently:

> **“kube-apiserver is the heart of Kubernetes; all components communicate through it.”**

