# 🚀 KinD Setup (Beginner Friendly)

## What is KinD?

* **KinD = Kubernetes in Docker**
* It runs **Kubernetes clusters inside Docker containers**
* Best for **local practice, testing, and learning**

---

## 🧠 Simple Analogy (Very Easy to Remember)

* **Docker** = Apartment building
* **KinD** = Renting one flat inside the building
* **Kubernetes cluster** = Family living inside the flat
* **Nodes** = Rooms in the flat

👉 KinD lets us practice Kubernetes **without real servers**.

---

## ✅ Step 1: Install KinD (Ubuntu)

Install using official link (3 commands only):
👉 [https://kind.sigs.k8s.io/docs/user/quick-start/#installation](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)

---

## ✅ Step 2: Install kubectl

* `kubectl` is used to **talk to the Kubernetes cluster**
  👉 [https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

---

## ✅ Step 3: Install Docker

* Docker is **mandatory** for KinD
  👉 [https://get.docker.com/](https://get.docker.com/)

---

# 🧱 Create KinD Cluster

```bash
kind create cluster
```

* Creates **default cluster**
* Name: `kind-kind`
* Single node (acts as **control plane + worker**)

---

## 🔍 Check Cluster Info

```bash
kubectl cluster-info
kubectl get nodes
```

* Confirms cluster is running
* Shows node status

---

## 📋 List KinD Clusters

```bash
kind get clusters
```

* Shows all running clusters

---

## 🔄 Create Custom Cluster Name

```bash
kind create cluster --name mycluster
```

* Creates cluster named `kind-mycluster`

---

## 🐳 Check Docker Containers

```bash
docker ps
```

* Each KinD cluster node is a **Docker container**

---

## 🖥️ Enter Cluster Node

```bash
docker exec -it kind-control-plane bash
```

* Enters Kubernetes node container

---

## ❌ Delete Cluster

```bash
kind delete cluster
kind delete cluster --name mycluster
```

---

# 🔁 Multiple KinD Clusters

```bash
kubectl config get-contexts
```

* Shows all cluster contexts

### Switch Cluster (like git branch)

```bash
kubectl config use-context kind-kind
kubectl config use-context kind-mycluster
```

👉 Similar to:

```bash
git checkout branch-name
```

---

# 🧩 Multi-Node KinD Cluster (Advanced)

### Create config file

```bash
vim /tmp/kind.yml
```

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: control-plane
- role: control-plane
- role: worker
```

### Create cluster

```bash
kind create cluster --config /tmp/kind.yml
```

* 3 control-plane + 1 worker
* High availability setup
* Auto load balancer inside Docker

---

## 🔢 Check Nodes

```bash
kubectl get nodes
docker ps
```

---

# ⏳ Deploy Older Kubernetes Version

```bash
kind create cluster --image kindest/node:v1.26.6
```

* Useful for **version testing**
* Image list:
  👉 [https://github.com/kubernetes-sigs/kind/releases](https://github.com/kubernetes-sigs/kind/releases)

---

# 💾 Dynamic Volume Provisioning

* KinD comes with **default StorageClass**

```bash
kubectl get sc
```

### Create PVC

```bash
kubectl create -f 4-pvc-nfs.yaml
kubectl get pv,pvc
```

### Attach PVC to Pod

```bash
kubectl create -f 4-busybox-pv-hostpath.yaml
```

* PVC gets **Bound** automatically

---

# 🌐 LoadBalancer in KinD (MetalLB)

KinD doesn’t support LoadBalancer by default.

### Install MetalLB

```bash
kubectl apply -f metallb-namespace.yaml
kubectl apply -f metallb.yaml
```

### Configure IP Range

```yaml
172.18.255.200-172.18.255.250
```

### Test

```bash
kubectl expose deploy nginx --type LoadBalancer --port 80
```

* Access app using assigned IP

---

## 🌍 Another Analogy (Interview Gold ⭐)

* **KinD** = Simulator
* **Real Kubernetes** = Real car
* We practice driving safely before going live 🚗

---

## ⭐ Interview Tip

Say this confidently:

> “KinD is used to run Kubernetes locally inside Docker, mainly for development, testing, and multi-cluster practice without cloud cost.”

