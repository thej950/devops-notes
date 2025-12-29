## 🧭 NodeAffinity, Taint, Toleration

- These three are used to **control where Pods run** in a Kubernetes cluster.

---

## 1️⃣ NodeAffinity (Pod chooses Node)

![Image](https://www.apptio.com/wp-content/uploads/how-node-affinity-works1.png) 

![Image](https://miro.medium.com/v2/resize%3Afit%3A1200/1%2AcJVojHLbVY4wgEc3W-Ie3g.png)

![Image](https://media.geeksforgeeks.org/wp-content/uploads/20240506110659/Node-Affinity-In-Kubernetes.webp)


### What is NodeAffinity?

* By default, **kube-scheduler** decides where Pods run.
* **NodeAffinity** lets **Pod choose a specific Node**.
* Pod runs only on nodes with **matching labels**.

### How it works (simple):

1. Add a **label** to a node (`key=value`)
2. In Pod/Deployment YAML, say:

   * “Run me only on nodes with this label”

### Example:

```bash
kubectl label node <node-name> slave1=thej1
```

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: slave1
              operator: In
              values:
                - thej1
```
➡️ Pod will run **only on that labeled node**

```
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: disk
          operator: In
          values: ["ssd"]
```



### Types of NodeAffinity

- **requiredDuringSchedulingIgnoredDuringExecution**
    - Must match (hard rule).
- **preferredDuringSchedulingIgnoredDuringExecution**
    - Nice to have (soft rule).

---

### Example 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    type: proxy
spec:
  containers:
    - name: mynginx
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: slave1
                operator: In
                values:
                  - thej1
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: niginx-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    type: proxy
```
> now nginx pod access through browser with 30008 port number 

### 🧠 NodeAffinity Analogy ⭐

* **Pod** = Employee
* **Node** = Office branch
* **Label** = Branch name

👉 Employee says: *“I want to work only in Hyderabad branch”*

**Interview:**
 - NodeAffinity allows Pods to be scheduled on specific nodes based on labels, giving more control over workload placement.

---

![Image](https://cdn.prod.website-files.com/635e4ccf77408db6bd802ae6/66e97f5bd21b67ce5de9c0bc_AD_4nXeqCCQ6sJ4RYjGxL7GnqmZXMPJOEkWZ2w91OrHWEBjRmWJRgT4ySmdzI1odcV-YBYxMvNP8j9AYV4lk99UkZ_fd4491rgj5c119k1BDmZwKPgbRbNDBjxMqGzIcHdIb2fE-Dh9pPmfQtamQ5xUx98r-Ewo-.png)

![Image](https://www.densify.com/wp-content/uploads/article-k8s-capacity-taint-tollerations.svg)

![Image](https://trstringer.com/images/prefernoschedule1.excalidraw.png)

## 2️⃣ Taint (Node rejects Pods)

### Simple meaning

* A **Taint** is applied on a **Node**.
* It tells Kubernetes: **“Do not schedule Pods here.”**
* Pods are **blocked** unless they have a **Toleration**.

---

### Why we use Taint

* To **protect special nodes**.
* Common for:

  * Database nodes
  * GPU nodes
  * Critical system nodes

---

### How Taint works (simple steps)

1. You add a taint to a Node.
2. Kubernetes blocks new Pods from that Node.
3. Only Pods with matching **Toleration** can run there.

---

### Taint syntax

```bash
kubectl taint nodes <node-name> key=value:NoSchedule
```

Example:

```bash
kubectl taint nodes node1 db=true:NoSchedule
```

---

### Taint Effects (Very Important)

* **NoSchedule** → New Pods will not be scheduled.
* **PreferNoSchedule** → Avoid scheduling if possible.
* **NoExecute** → Existing Pods are removed.

---

### What is a Taint?

* **Taint is applied on a Node**
* It tells Kubernetes:

  * ❌ “Do NOT schedule Pods here”

### Syntax:

```bash
kubectl taint nodes <node-name> slave1=thej1:NoSchedule
```

* `NoSchedule` → new Pods are blocked
* Existing Pods are **not affected**

### Why use Taints?

* Mostly for **database nodes**
* Prevent other apps from running there

---

### Example 
 - How to taint a machine in cluster
 
 ```bash
 kubectl taint nodes take_node_id slave1=thej1:NoSchedule # from above command taint will be applied with key=value:NoShedule specfications 
 ``` 

### To see tainted machine in cluster 

```bash
kubectl get nodes -o custom-columns=NAME:.metadata.name,TAINTS:.spec.taints
```

### Add taint and Remove Taint on node 
```bash
kubectl taint node id_machine slave2=intelliit2:NoShedule  # it will add 
kubectl taint nodes id_machine slave2=intellit2:NoShedule- # it will remove 
```




### 🧠 Taint Analogy ⭐

* **Node** = VIP room
* **Taint** = “No Entry” board 🚫

👉 Only special people can enter

### Important Note

* Taint affects **future Pods only**.
* Existing Pods stay unless effect is **NoExecute**.

---

### One-line summary

* **Taint = Node blocks Pods from being scheduled.**

---

### ⭐ Interview Tip

Say this clearly:

> “A taint is applied to a node to repel Pods, allowing only Pods with matching tolerations to be scheduled on that node.”

---

![Image](https://cdn.hashnode.com/res/hashnode/image/upload/v1724868429627/294a6741-5c32-4116-8db8-a2678af0c3d9.png?auto=compress%2Cformat\&format=webp)


## 3️⃣ Toleration (Pod accepts Taint)

### What is Toleration?

* **Toleration is applied on Pod**
* It allows Pod to **run on a tainted node**

### Example:

```yaml
tolerations:
  - key: slave2
    operator: Equal
    value: thej2
    effect: NoSchedule
```

➡️ Pod is now **allowed** to run on tainted node

---

### 🧠 Toleration Analogy ⭐

* **Pod** = Person with special pass 🎫
* **Tainted Node** = Restricted area

👉 Only pass holders are allowed

---

## 🔁 Important Rules (Easy to Remember)

| Feature      | Applied On | Purpose                     |
| ------------ | ---------- | --------------------------- |
| NodeAffinity | Pod        | Pod selects node            |
| Taint        | Node       | Node blocks Pods            |
| Toleration   | Pod        | Pod allowed on tainted node |

---

## ⚠️ Very Important Point

* If a Pod is already running
* And later Node is tainted
* ❗ Pod will **NOT be removed**
* Taint affects **only future Pods**

---

### Simple meaning

* **Toleration** is applied on a **Pod**.
* It allows the Pod to **run on a tainted Node**.
* Without toleration → Pod is **blocked** from that Node.

👉 **Taint = Node says NO**
👉 **Toleration = Pod says I can handle it**

### Why we use Toleration

* To allow **special Pods** on **restricted Nodes**.
* Common use cases:

  * Database Pods
  * Monitoring agents
  * System or critical apps

---

### How it works (easy steps)

1. Node has a **taint** (`key=value:effect`).
2. Pod has a **matching toleration**.
3. Kubernetes allows the Pod to run on that Node.

---

### Simple Example (YAML)

```yaml
tolerations:
  - key: "db"
    operator: "Equal"
    value: "true"
    effect: "NoSchedule"
```

👉 This Pod can run on nodes tainted with `db=true:NoSchedule`.

---

### Toleration Effects (Important)

* **NoSchedule** → New Pods blocked unless tolerated
* **PreferNoSchedule** → Avoid if possible
* **NoExecute** → Existing Pods removed unless tolerated

---

## Example: 

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
  labels:
    type: webserver
spec:
  containers:
    - name: myhttpd
      image: httpd
  tolerations:
    - key: slave2
      operator: Equal
      value: thej2 
      effect: NoSchedule

...
```
> From Above file we apply toleration on tainted machine then httpd pod runs on slave2 machine 

## 🧠 One Final Analogy (Interview Gold ⭐)

* **NodeAffinity** → Pod says *“I like this node”* ❤️
* **Taint** → Node says *“I don’t like Pods”* 🚫
* **Toleration** → Pod says *“I can handle your taint”* ✅

---

## ⭐ Interview Tip

Say this clearly:

> “NodeAffinity controls where Pods prefer to run, Taints repel Pods from nodes, and Tolerations allow Pods to run on tainted nodes.”



