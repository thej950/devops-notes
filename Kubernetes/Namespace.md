![Image](https://cdn.prod.website-files.com/6527fe8ad7301efb15574cc7/654cd76416fd0c35c9a3cf12_diagrams-V2-01-1024x576.png)

## 📦 Namespace in Kubernetes

### What is a Namespace?

* A **Namespace** is a **logical partition** inside a Kubernetes cluster.
* It helps to **separate resources** (Pods, Services, Deployments).
* Used to **organize, isolate, and manage** applications.

👉 One cluster, **multiple namespaces**.

---

### Default Namespaces in Kubernetes

When Kubernetes is installed, **4 namespaces** are created:

1. **default** → your apps run here if you don’t specify namespace
2. **kube-system** → core Kubernetes components
3. **kube-public** → public cluster info
4. **kube-node-lease** → node heartbeat info

---

### What happens if you don’t mention namespace?

```bash
kubectl run webserver1 --image=nginx
```

* Pod is created in **default** namespace.
* Kubernetes always assumes **default** if not specified.

---

### What runs in kube-system namespace?

These are **background cluster services**:

* kube-apiserver
* kube-scheduler
* controller-manager
* etcd
* kubelet
* kube-proxy

👉 These are **not user applications**.

---

## Why do we use Namespace?

* Separate **dev / test / prod**
* Avoid **name conflicts**
* Apply **RBAC and resource limits**
* Better cluster management

---

## 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Kubernetes Cluster** = Office Building
* **Namespaces** = Different departments
* **Pods** = Employees

👉 HR, Finance, IT work separately but inside same building.

---

## Create a Custom Namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: test-ns
```

```bash
kubectl apply -f namespace.yml
kubectl get namespaces
```

---

## Create Pod Inside Custom Namespace

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ghost-pod
  namespace: test-ns
spec:
  containers:
  - name: ghost-app
    image: ghost
    env:
    - name: NODE_ENV
      value: development
```

```bash
kubectl apply -f pod-definition5.yml
```

---

## View Pods

```bash
kubectl get pods              # default namespace
kubectl get pods -n test-ns   # custom namespace
```

---

## Delete Namespace

```bash
kubectl delete -f namespace.yml
```

⚠️ Deleting a namespace deletes **all resources inside it**.

---

## 🔑 One-Line Summary

* **Namespace** = logical separation inside a Kubernetes cluster.

---

## ⭐ Interview Tip

Say this clearly:

> “A namespace is a logical isolation mechanism in Kubernetes used to organize resources and separate environments within the same cluster.”
