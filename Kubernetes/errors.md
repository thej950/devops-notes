# 📌 Main Points

### 1. **Pod & Container Failures**
- Common errors: `CrashLoopBackOff`, `RunContainerError`, `OOMKilled`.  
- Causes: app crashes, misconfigured entrypoints, memory limits exceeded.  
- Fixes: check logs, correct configs, adjust resource limits.

### 2. **Image & Registry Issues**
- Errors: `ImagePullBackOff`, `ErrImagePull`, `InvalidImageName`.  
- Causes: wrong image names, missing credentials, malformed references.  
- Fixes: verify image URLs, registry secrets, use versioned tags.

### 3. **Configuration Errors**
- Errors: invalid env/config, missing `ConfigMap` or `Secret`.  
- Causes: misconfigured references or missing resources.  
- Fixes: correct references, recreate missing resources.

### 4. **Scheduling & Resource Problems**
- Errors: `PodUnschedulable`, `FailedScheduling`, `NodeNotReady`.  
- Causes: insufficient resources, taints, node failures.  
- Fixes: adjust node pools, affinities, quotas, restart nodes.

### 5. **Storage & Volume Issues**
- Errors: `PVC Pending`, `VolumeMountError`, `Multi-Attach error`.  
- Causes: no matching PVs, incorrect mount paths, conflicting volume usage.  
- Fixes: provision PVs, check access modes, detach conflicting volumes.

### 6. **Networking & DNS Failures**
- Errors: service unreachable, DNS resolution failed, Ingress 404.  
- Causes: misconfigured selectors, CoreDNS crashes, wrong Ingress paths.  
- Fixes: correct service specs, restart DNS, fix Ingress rules.

### 7. **Security & Access Problems**
- Errors: RBAC denied, unauthorized, PodSecurityPolicy violation.  
- Causes: insufficient permissions, policy conflicts.  
- Fixes: add proper roles, renew credentials, adjust policies.

### 8. **Deployment & Scaling Issues**
- Errors: rollout stuck, HPA not scaling, Helm release failed.  
- Causes: misconfigured probes, missing metrics-server, Helm chart errors.  
- Fixes: validate templates, install metrics-server, fix probes.

### 9. **Cluster & Node Failures**
- Errors: node disk full, API server unreachable, kube-proxy fails.  
- Causes: resource exhaustion, network/cert issues, corrupted iptables.  
- Fixes: clean logs, check control plane, restore iptables.

### 10. **Miscellaneous Errors**
- Errors: invalid YAML, job deadline exceeded, pod never terminates.  
- Causes: syntax errors, logic flaws, missing timeouts.  
- Fixes: lint YAML, optimize job logic, add termination checks.

---

### 🎯 Key Takeaway
This document is a **Kubernetes troubleshooting handbook**:  
- Maps **errors → root causes → fixes**.  
- Emphasizes checking logs/configs first.  
- Validating resource references (images, secrets, ConfigMaps).  
- Managing cluster resources (nodes, memory, storage).  
- Ensuring networking, RBAC, and policies are correct.  
- Following best practices like versioned images, quotas, and monitoring tools.

---

- **k8s all errors list**[[click](https://drive.google.com/file/d/1u4Kyd5G8l6aCfMrrE8NI0XtKnA8__dNe/view?usp=sharing)]

![Image](https://cdn.prod.website-files.com/681e366f54a6e3ce87159ca4/68757f9e61edb27b795c9588_What-is-Crashloopbackoff-02.png)

# 1. 🔁 CrashLoopBackOff

### What is CrashLoopBackOff?

* **CrashLoopBackOff** means a **Pod keeps crashing and restarting again and again**.
* Kubernetes tries to start the container.
* The container fails.
* Kubernetes waits a bit and **tries again** (back-off).
* This loop continues → **CrashLoopBackOff**.

---

#### Why does CrashLoopBackOff happen? (Common Reasons)

* Wrong **command or entrypoint**
* Application **crashes at startup**
* Missing or wrong **environment variables**
* App can’t connect to **DB / service**
* **Memory limit too low** (OOMKilled)
* Wrong **image or version**

---

### How Kubernetes behaves

1. Pod starts.
2. Container crashes.
3. Kubernetes restarts it.
4. Crash happens again.
5. Kubernetes slows down restarts → *BackOff*

---

### How to check the issue

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
```

* **describe** → shows events and errors
* **logs** → shows application error

---

### 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Pod** = Mobile phone
* **App** = App you open

👉 You open the app
👉 App crashes immediately
👉 Phone keeps reopening it
👉 Finally phone slows retries

That is **CrashLoopBackOff** 📱

---

### Simple Example

* MySQL Pod starts
* Password env variable is missing
* MySQL exits
* Kubernetes restarts it
* Result → CrashLoopBackOff

---

### Why CrashLoopBackOff happens?

* Wrong command / entrypoint
* Missing environment variables
* App fails to start
* DB connection failure
* Wrong image


### How to fix it (Basic Steps)

* Check **logs**
* Verify **env variables**
* Increase **memory limits**
* Fix **command / image**
* Ensure dependent services are up

---

### 🔑 One-Line Summary

* **CrashLoopBackOff** = Pod is stuck in a crash + restart loop.

---

### ⭐ Interview Tip

Say this clearly:

> “CrashLoopBackOff occurs when a container keeps crashing on startup and Kubernetes repeatedly tries to restart it with a back-off delay.”

---

![Image](https://miro.medium.com/1%2Ak1AllMAcwtEJiA0gTgMC7Q.png)

# 2. 🔴 What is **OOMKilled**?

* **OOMKilled** means **Out Of Memory Killed**.
* Container used **more memory than its limit**.
* Kubernetes **kills the container immediately**.
* It is a **memory problem**.

### Why OOMKilled happens?

* Memory limit is too low
* Application uses more RAM
* Memory leak in application

👉 Example:
Limit = 128Mi, App uses = 200Mi → **OOMKilled**

---

### 🆚 OOMKilled vs CrashLoopBackOff (Clear Table)

| Feature             | OOMKilled             | CrashLoopBackOff            |
| ------------------- | --------------------- | --------------------------- |
| Problem type        | Memory issue          | App / config issue          |
| Reason              | Memory limit exceeded | App crashes on start        |
| Container killed by | Kernel / Kubernetes   | Application failure         |
| Restart behavior    | Killed immediately    | Repeated restart with delay |
| Common fix          | Increase memory limit | Fix config / logs           |

---

### 🧠 Very Easy Analogy (Interview Gold ⭐)

* **OOMKilled** = Phone battery full usage 🔋

  * App eats all RAM
  * Phone force closes app

* **CrashLoopBackOff** = Buggy app 📱

  * App opens
  * App crashes
  * Phone retries again and again

---

### 🔧 How to Troubleshoot

### For OOMKilled:

```bash
kubectl describe pod <pod-name>
```

* Look for `OOMKilled`
* Increase memory limits

---

### For CrashLoopBackOff:

```bash
kubectl logs <pod-name>
kubectl logs <pod-name> --previous
kubectl describe pod <pod-name>
```

* Fix application error

---

### 🔑 One-Line Difference (Easy to Remember)

* **OOMKilled** → memory exceeded
* **CrashLoopBackOff** → app keeps crashing

---

### ⭐ Interview Tip

Say this clearly:

> “OOMKilled happens when a container exceeds its memory limit, while CrashLoopBackOff happens when a container repeatedly crashes during startup.”

# 🚫 `ImagePullBackOff`, `ErrImagePull`, `InvalidImageName`

These errors mean **Kubernetes cannot download the container image**.

---

## 1. 🔴 `ErrImagePull`

### What it means

* Kubernetes **tried to pull the image and failed**.

### Common reasons

* Image name or tag is wrong
* Docker Hub / registry is down
* Private image but **no imagePullSecret**

### How to check

```bash
kubectl describe pod <pod-name>
```

---

## 2. 🔴 `ImagePullBackOff`

### What it means

* Kubernetes **already failed many times** to pull the image.
* Now it **waits and retries slowly** (back-off).

### Important

* This usually comes **after `ErrImagePull`**.

---

## 3. 🔴 `InvalidImageName`

### What it means

* Image name format is **invalid**.
* Kubernetes cannot even try to pull it.

### Examples of wrong names

* `nginx:` (tag missing)
* `Nginx` (case-sensitive issue)
* `nginx@@latest` (invalid characters)

---

### 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Kubernetes** = Zomato delivery app
* **Image** = Food item

👉 **InvalidImageName** = You typed food name wrong ❌
👉 **ErrImagePull** = Restaurant not reachable 🚫
👉 **ImagePullBackOff** = App waits and retries ⏳

---

### 🆚 Quick Comparison Table

| Error            | Meaning                 |
| ---------------- | ----------------------- |
| InvalidImageName | Image name is wrong     |
| ErrImagePull     | Image pull failed       |
| ImagePullBackOff | Retrying after failures |

---

### 🔧 How to Fix (Quick Steps)

1. Check image name and tag
2. Try `docker pull <image>`
3. Verify private registry credentials
4. Check network / proxy
5. Use correct `imagePullSecrets`

---

### 🔑 One-Line Summary

* **InvalidImageName** → wrong image format
* **ErrImagePull** → failed to download image
* **ImagePullBackOff** → Kubernetes waiting before retry

---

### ⭐ Interview Tip

Say this clearly:

> “ErrImagePull means image download failed, ImagePullBackOff means Kubernetes is retrying with delay, and InvalidImageName means the image name itself is wrong.”

# ❌ Errors: **invalid env/config**, **missing ConfigMap / Secret**

These errors happen when a Pod **expects configuration**, but Kubernetes **can’t find it or it’s wrong**.

---

## 1. 🔴 Invalid env / config

### What it means

* The **environment variable value is wrong** or **not usable**.
* App starts → reads config → **fails and exits**.

### Common reasons

* Wrong variable name (typo)
* Wrong value format (string vs number)
* Missing required env variable
* App expects config file but it’s empty

### How it looks

* Pod may go to **CrashLoopBackOff**
* Logs show config or startup error

### Check

```bash
kubectl logs <pod-name>
kubectl describe pod <pod-name>
```

---

## 2. 🔴 Missing ConfigMap

### What it means

* Pod refers to a **ConfigMap that does not exist**.
* Kubernetes cannot inject env or mount files.

### Common reasons

* ConfigMap not created
* Wrong name in Pod YAML
* Wrong namespace

### Example error

* `configmap "app-config" not found`

### Fix

```bash
kubectl get configmap
kubectl get configmap -n <namespace>
```

---

## 3. 🔴 Missing Secret

### What it means

* Pod refers to a **Secret that does not exist**.
* Usually affects DB passwords, API keys.

### Common reasons

* Secret not created
* Wrong secret name/key
* Secret exists in another namespace

### Example error

* `secret "db-secret" not found`

### Fix

```bash
kubectl get secret
kubectl get secret -n <namespace>
```

---

### 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Application** = Person
* **ConfigMap** = Instructions paper 📄
* **Secret** = Locker key 🔐

👉 No instructions → person confused
👉 No key → locker won’t open
👉 Result → work fails ❌

---

### 🧪 Quick YAML Example (Env from ConfigMap/Secret)

```yaml
env:
- name: DB_HOST
  valueFrom:
    configMapKeyRef:
      name: app-config
      key: database_url
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: password
```

---

### 🔧 Troubleshooting Checklist

1. Check Pod events: `kubectl describe pod`
2. Verify names and keys (case-sensitive)
3. Ensure **same namespace**
4. Check logs for config errors
5. Create missing ConfigMap/Secret

---

### 🔑 One-Line Summary

* **Invalid env/config** → wrong or missing values
* **Missing ConfigMap/Secret** → referenced object not found

---

### ⭐ Interview Tip

Say this clearly:

> “These errors occur when Pods reference incorrect or missing ConfigMaps or Secrets, causing applications to fail during startup.”


![Image](https://img.site24x7static.com/images/without-set-requests-limits-consume-memory-resulting-failure-pod.png)

![Image](https://komodor.com/wp-content/uploads/2022/02/Kubernetes-Troubleshooting_-4.png)

![Image](https://labs.iximiuz.com/content/files/challenges/kubernetes-pod-debugging-part-1-ae00ba45/__static__/pod-lifecycle.png)

# ❌ `PodUnschedulable`, `FailedScheduling`, `NodeNotReady`

These errors mean **Kubernetes cannot place a Pod on any node** or **nodes are not healthy**.

---

## 1. 🔴 `FailedScheduling`

### What it means

* Kubernetes **tried to schedule the Pod** but **no node matched the requirements**.

### Common reasons

* Not enough **CPU or memory**
* Node selectors / taints not matching
* Requested resources > node capacity
* PVC not bound
* HostPort already in use

### How to check

```bash
kubectl describe pod <pod-name>
kubectl get nodes
```

---

## 2. 🔴 `PodUnschedulable`

### What it means

* Pod is in **Pending** state.
* Scheduler **cannot find a suitable node**.

👉 This is usually the **status/result** of `FailedScheduling`.

---

## 3. 🔴 `NodeNotReady`

### What it means

* Node is **unhealthy or unreachable**.
* Kubernetes **will not schedule Pods** on this node.

### Common reasons

* Kubelet stopped
* Disk / memory pressure
* Network issue
* Node rebooted or down

### How to check

```bash
kubectl get nodes
kubectl describe node <node-name>
```

---

### 🧠 Very Easy Analogy (Interview Gold ⭐)

* **Pods** = Passengers
* **Nodes** = Buses 🚌

👉 **FailedScheduling** = No bus has empty seats
👉 **PodUnschedulable** = Passenger waiting at bus stop
👉 **NodeNotReady** = Bus is broken

---

### 🆚 Quick Comparison Table

| Error            | Meaning                    |
| ---------------- | -------------------------- |
| FailedScheduling | No suitable node found     |
| PodUnschedulable | Pod waiting (result state) |
| NodeNotReady     | Node is unhealthy          |

---

### 🔧 How to Fix (Quick Steps)

1. Check node status (`kubectl get nodes`)
2. Reduce resource requests
3. Add more nodes
4. Fix taints / tolerations
5. Check PVC binding
6. Restart kubelet on node

---

### 🔑 One-Line Summary

* **FailedScheduling** → scheduler can’t place Pod
* **PodUnschedulable** → Pod waiting to be scheduled
* **NodeNotReady** → node is unhealthy

---

### ⭐ Interview Tip

Say this clearly:

> “These errors indicate scheduling issues where either no suitable node is available or the node itself is not in a ready state.”


# ❌ `PVC Pending`, `VolumeMountError`, `Multi-Attach error`


These errors are related to **storage (Persistent Volumes)** in Kubernetes.

---

## 1. 🔴 `PVC Pending`

### What it means

* **PersistentVolumeClaim (PVC)** is created
* But **no PersistentVolume (PV) is available** to bind it

### Common reasons

* No PV exists
* StorageClass is missing or wrong
* Storage size mismatch
* Dynamic provisioner not running

### How to check

```bash
kubectl get pvc
kubectl get pv
kubectl describe pvc <pvc-name>
```

---

## 2. 🔴 `VolumeMountError`

### What it means

* Volume exists, but **cannot be mounted inside the Pod**

### Common reasons

* Wrong mountPath
* Permission issues
* Node cannot access storage
* CSI driver issue

### Result

* Pod may stay in **ContainerCreating**
* App does not start

### How to check

```bash
kubectl describe pod <pod-name>
kubectl get events
```

---

## 3. 🔴 `Multi-Attach error`

### What it means

* Same volume is being **attached to multiple Pods at the same time**
* Volume supports only **ReadWriteOnce (RWO)**

### Common scenario

* Using Deployment with replicas > 1
* Single PVC attached to multiple Pods

### Example error

* `Multi-Attach error for volume`

---

### 🧠 Very Easy Analogy (Interview Gold ⭐)

* **PVC** = Parking request 🚗
* **PV** = Parking slot

👉 **PVC Pending** = No parking slot available
👉 **VolumeMountError** = Slot exists but gate is locked
👉 **Multi-Attach error** = One slot, two cars fighting

---

### 🆚 Quick Comparison Table

| Error              | Meaning                       |
| ------------------ | ----------------------------- |
| PVC Pending        | No volume available           |
| VolumeMountError   | Volume can’t be attached      |
| Multi-Attach error | Same volume used by many Pods |

---

### 🔧 How to Fix (Quick Steps)

1. Create correct PV or StorageClass
2. Match PVC size and access mode
3. Use **StatefulSet** for databases
4. Use one PVC per Pod
5. Check CSI driver & node health

---

### 🔑 One-Line Summary

* **PVC Pending** → waiting for storage
* **VolumeMountError** → storage mount failed
* **Multi-Attach error** → volume used by multiple Pods

---

### ⭐ Interview Tip

Say this clearly:

> “These errors occur due to storage issues like missing volumes, mount failures, or attaching the same volume to multiple Pods.”

# 🌐 Networking Errors

## 1. 🔴 Service Unreachable

**What it means**

* Service exists, but traffic **cannot reach Pods**.

**Common reasons**

* Service selector labels don’t match Pod labels
* Pod is not running / not Ready
* Wrong Service type or port

**Check**

```bash
kubectl get svc
kubectl get pods --show-labels
kubectl describe svc <svc-name>
```

---

## 2. 🔴 DNS Resolution Failed

**What it means**

* Pod cannot resolve service name (DNS issue).

**Common reasons**

* CoreDNS not running
* Wrong service name / namespace
* Network policy blocking DNS

**Check**

```bash
kubectl get pods -n kube-system
kubectl logs -n kube-system deploy/coredns
```

---

## 3. 🔴 Ingress 404

**What it means**

* Ingress is reachable, but **path/host not matched**.

**Common reasons**

* Wrong host/path in Ingress
* Backend Service name/port wrong
* Ingress controller not running

**Check**

```bash
kubectl get ingress
kubectl describe ingress <ingress-name>
kubectl get pods -n ingress-nginx
```

---

# 🔐 Security / Access Errors

### 1. 🔴 RBAC Denied / Unauthorized

**What it means**

* User/ServiceAccount **does not have permission**.

**Common reasons**

* Missing Role/ClusterRole
* Missing RoleBinding/ClusterRoleBinding

**Check**

```bash
kubectl auth can-i get pods
kubectl describe rolebinding
```

---

### 2. 🔴 PodSecurityPolicy Violation

**What it means**

* Pod violates **security rules**.

**Common reasons**

* Running as root
* Privileged container
* HostPath not allowed

**Fix**

* Update security context
* Use allowed settings only

---

# 🚀 Deployment / Scaling / Helm Errors

### 1. 🔴 Rollout Stuck

**What it means**

* Deployment update **not finishing**.

**Common reasons**

* New Pods failing (CrashLoopBackOff)
* Readiness probe failing
* Image pull issues

**Check**

```bash
kubectl rollout status deploy <name>
kubectl describe deploy <name>
```

---

### 2. 🔴 HPA Not Scaling

**What it means**

* Pods **not increasing/decreasing**.

**Common reasons**

* metrics-server missing
* CPU requests not set
* Low traffic / wrong threshold

**Check**

```bash
kubectl get hpa
kubectl top pods
```

---

### 3. 🔴 Helm Release Failed

**What it means**

* Helm chart install/upgrade **did not succeed**.

**Common reasons**

* YAML error
* Resource already exists
* Missing values
* Permission issues

**Check**

```bash
helm status <release>
helm get all <release>
helm install --debug --dry-run
```

---

### 🧠 Super Easy Analogy (Interview Gold ⭐)

* **Service unreachable** → Phone number correct, phone switched off 📵
* **DNS failed** → Name not in contacts 📒
* **Ingress 404** → Wrong door in building 🚪
* **RBAC denied** → No office access card 🚫
* **Rollout stuck** → New staff not joining 🧍
* **HPA not scaling** → Manager can’t see workload 📊
* **Helm failed** → App installer crashed 💥

---

### 🔑 One-Line Summary

* Network errors → traffic/DNS/Ingress issues
* Security errors → permission/policy issues
* Ops errors → rollout, scaling, Helm problems

---

### ⭐ Interview Tip

Say this clearly:

> “Most Kubernetes errors fall into networking, security, or deployment categories, and the first step is always checking `kubectl describe`, logs, and events.”
