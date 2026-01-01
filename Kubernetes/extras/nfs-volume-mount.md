![Image](https://www.itwonderlab.com/wp-content/uploads/tutorials/Kubernetes-NFS/Kubernetes_PVC_NFS.png)

![Image](https://matthewpalmer.net/kubernetes-app-developer/articles/volume-mount-diagram.png)

Below is a **clean, corrected, and production-friendly version** of your NFS volume setup in Kubernetes.
I’ve also **highlighted common mistakes and missing best practices** clearly.

---

# 📦 Mounting an NFS Volume in Kubernetes (Beginner-Friendly)

Kubernetes does **not create NFS storage automatically**.
You must **pre-create NFS storage** and then connect it to Pods using **PV → PVC → Pod**.

---

## 🟢 Step 0: Prerequisites (Very Important)

Before Kubernetes YAMLs:

* NFS server must be **running**
* Export path must exist
* Worker nodes must be able to **reach NFS server**
* Install NFS client on nodes:

```bash
sudo apt install nfs-common -y   # Ubuntu
sudo yum install nfs-utils -y    # RHEL/Amazon Linux
```

⚠️ **Common mistake:**
PVC stuck in `Pending` because NFS client is missing on nodes.

---

## 🟢 Step 1: Create PersistentVolume (PV)

📄 `nfs-pv.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  storageClassName: nfs-storage
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  nfs:
    server: 192.168.1.100   # NFS server IP
    path: /exports/data     # Exported path
```

### Explanation

* **PV** = actual storage
* `ReadWriteMany` → multiple Pods can mount
* `Retain` → data not deleted if PVC is deleted

🧠 **Analogy:**
PV = **Shared network drive**

---

## 🟢 Step 2: Create PersistentVolumeClaim (PVC)

📄 `nfs-pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc
spec:
  storageClassName: nfs-storage
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
```

### Explanation

* PVC is a **request** for storage
* Must match:

  * `storageClassName`
  * `accessModes`

🧠 **Analogy:**
PVC = **Request form for shared storage**

⚠️ **Common mistake:**
If storageClassName does not match → PVC stays `Pending`.

---

## 🟢 Step 3: Create Pod Using NFS PVC

📄 `nfs-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nfs-pod
spec:
  containers:
    - name: app
      image: nginx
      volumeMounts:
        - name: nfs-volume
          mountPath: /usr/share/nginx/html
  volumes:
    - name: nfs-volume
      persistentVolumeClaim:
        claimName: nfs-pvc
```

### Explanation

* PVC is mounted inside container
* Data written here is stored on **NFS server**
* Pod restart ❌ data loss → **NO**

🧠 **Analogy:**
Pod using NFS = **Multiple laptops using same Google Drive**

---

## 🟢 Step 4: Apply Resources (Correct Order)

```bash
kubectl apply -f nfs-pv.yaml
kubectl apply -f nfs-pvc.yaml
kubectl apply -f nfs-pod.yaml
```

Verify:

```bash
kubectl get pv
kubectl get pvc
kubectl get pods
```

Expected:

* PV → `Bound`
* PVC → `Bound`
* Pod → `Running`

---

## ❌ Common Mistakes (Very Important)

### ❌ Wrong server IP

* Pod stuck in `ContainerCreating`
* Fix: Verify NFS server IP & firewall

---

### ❌ Missing RWX support

* Using cloud disk (EBS) instead of NFS
* RWX works only with NFS / EFS / Azure Files

---

### ❌ No `Retain` policy

* Data deleted accidentally when PVC deleted

---

### ❌ Using Pod instead of Deployment

* Pods are not self-healing
* Use Deployment/StatefulSet in real projects

---

## 🟡 Production Best Practices (Extra Knowledge)

✅ Use **StatefulSet** for databases
✅ Use **NFS CSI driver** instead of static PV
✅ Add **SecurityContext** if permission issues
✅ Monitor NFS performance (latency sensitive)

---

## 🔑 One-Line Interview Answer

> “To mount NFS in Kubernetes, we create a PersistentVolume pointing to the NFS server, bind it using a PVC, and mount that PVC inside a Pod. This provides shared, persistent storage.”

---

## ⭐ Interview Tip

If interviewer asks **“Why NFS?”**, say:

> “NFS supports ReadWriteMany, so multiple Pods can share the same data across nodes.”

---

![Image](https://hpe-developer-portal.s3.amazonaws.com/uploads/media/2020/6/csi-120-rev1b-1592687082644.png)

![Image](https://www.simplyblock.io/wp-content/media/Dynamic-Provisioning-in-Kubernetes.png)

Below is the **PRODUCTION-READY way to use NFS in Kubernetes using the CSI driver**.
This is how it’s done in **real projects**, not lab-style static PVs.

---

# 🚀 NFS CSI Driver (Production Way – Simple English)

## Why CSI instead of normal NFS PV?

❌ Static PV = manual, not scalable
✅ **CSI Driver = dynamic provisioning, production-ready**

🧠 **Analogy:**
Static PV = manually booking rooms
CSI Driver = **hotel reception auto-assigns rooms**

---

## 🟢 Architecture (High Level)

```
Pod → PVC → StorageClass → NFS CSI Driver → NFS Server
```

* CSI driver talks to NFS
* Storage is created **automatically**
* No need to create PV manually

---

## 🟢 Step 0: Prerequisites

* NFS server running
* Export path exists (example: `/exports/k8s`)
* Worker nodes have NFS client installed:

```bash
sudo apt install nfs-common -y
```

---

## 🟢 Step 1: Install NFS CSI Driver

Official driver: **nfs.csi.k8s.io**

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/install-driver.yaml
```

Verify:

```bash
kubectl get pods -n kube-system | grep nfs
```

Expected:

* `csi-nfs-controller`
* `csi-nfs-node`

🧠 **Analogy:**
CSI driver = **Translator between Kubernetes and NFS**

---

## 🟢 Step 2: Create StorageClass (MOST IMPORTANT)

📄 `nfs-storageclass.yaml`

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs-csi
provisioner: nfs.csi.k8s.io
parameters:
  server: 192.168.1.100        # NFS server IP
  share: /exports/k8s          # NFS export path
reclaimPolicy: Retain
volumeBindingMode: Immediate
```

Apply:

```bash
kubectl apply -f nfs-storageclass.yaml
```

🧠 **Analogy:**
StorageClass = **Rule book for creating storage**

---

## 🟢 Step 3: Create PVC (Dynamic – No PV!)

📄 `nfs-pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pvc
spec:
  storageClassName: nfs-csi
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
```

Apply:

```bash
kubectl apply -f nfs-pvc.yaml
```

Check:

```bash
kubectl get pvc
kubectl get pv
```

✅ PV is **AUTO-CREATED**

🧠 **Analogy:**
PVC = **Online order**, PV = **auto-delivery**

---

## 🟢 Step 4: Use PVC in Deployment / StatefulSet

📄 `nginx-deploy.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx
          volumeMounts:
            - name: nfs-vol
              mountPath: /usr/share/nginx/html
      volumes:
        - name: nfs-vol
          persistentVolumeClaim:
            claimName: nfs-pvc
```

Apply:

```bash
kubectl apply -f nginx-deploy.yaml
```

🧠 **Analogy:**
Multiple Pods = **multiple laptops using same shared drive**

---

## 🟢 Step 5: Verify (Production Checks)

```bash
kubectl get pods
kubectl describe pvc nfs-pvc
kubectl exec -it <pod-name> -- df -h
```

* Data shared across Pods
* Pod restart ❌ data loss → **NO**
* Node change ❌ issue → **NO**

---

## 🔐 Production Best Practices (VERY IMPORTANT)

✅ Use **RWX** only when needed
✅ Use **StatefulSet** for DBs
✅ Use **Retain** reclaim policy
✅ Secure NFS exports (IP allowlist)
✅ Monitor latency (NFS is network-based)

---

## ❌ Common Production Mistakes

❌ Using HostPath in prod
❌ Using static PV for dynamic workloads
❌ Forgetting NFS client on nodes
❌ Using NFS for high-IO databases blindly

---

## 🔑 Interview-Ready One-Line Answer

> “In production, NFS is integrated using the NFS CSI driver with a StorageClass, enabling dynamic provisioning of RWX volumes without manual PV creation.”

---

## ⭐ Interview Tip

If asked **“Static PV vs CSI?”**, say:

> “Static PVs are manual and not scalable, whereas CSI drivers enable dynamic, cloud-native storage provisioning.”

---

![Image](https://hpe-developer-portal.s3.amazonaws.com/uploads/media/2020/6/csi-120-rev1b-1592687082644.png)

![Image](https://www.opcito.com/sites/default/files/inline-images/EFS%20CSI%20Driver%20.png)

![Image](https://miro.medium.com/1%2AGig18TiVgWdxXeedc778UA.png)

Below is a **clear, interview-ready comparison of NFS CSI vs EFS CSI**, written in **simple English**, with **real-world usage** and **easy analogy**.

---

# 🔍 NFS CSI vs EFS CSI (Kubernetes Storage)

## 🔹 What they are (1 line each)

### NFS CSI

* Uses **your own NFS server** (on-prem or VM) as shared storage.

🧠 Analogy:
**Office file server** inside your company.

---

### EFS CSI

* Uses **AWS managed file system (EFS)** as shared storage.

🧠 Analogy:
**Google Drive managed by AWS**.

---

## 🔸 Core Comparison Table

| Feature           | NFS CSI Driver        | EFS CSI Driver      |
| ----------------- | --------------------- | ------------------- |
| Storage Type      | Self-managed NFS      | AWS managed EFS     |
| Cloud Dependency  | Any (on-prem / cloud) | AWS only            |
| Setup Effort      | Medium / High         | Very low            |
| Maintenance       | You manage server     | AWS manages         |
| RWX Support       | ✅ Yes                 | ✅ Yes               |
| High Availability | ❌ Manual              | ✅ Built-in          |
| Scaling           | ❌ Manual              | ✅ Automatic         |
| Cost              | Low                   | Higher              |
| Performance       | Depends on server     | High & elastic      |
| Production Ready  | ⚠️ Yes (with care)    | ✅ Yes (recommended) |

---

## 🔹 Architecture Difference (Simple)

### NFS CSI

```
Pod → PVC → StorageClass → NFS CSI → Your NFS Server
```

### EFS CSI

```
Pod → PVC → StorageClass → EFS CSI → AWS EFS (Multi-AZ)
```

---

## 🔹 When to Use NFS CSI

✅ On-prem Kubernetes
✅ Budget-sensitive projects
✅ Dev / Test environments
✅ Small to medium workloads

❌ Not ideal for:

* Large scale
* Auto scaling
* Multi-AZ without effort

🧠 Analogy:
NFS CSI = **You own the fridge and fix it yourself**

---

## 🔹 When to Use EFS CSI

✅ AWS EKS production
✅ Microservices needing shared storage
✅ Multi-AZ workloads
✅ Zero maintenance storage

❌ Not ideal for:

* Tight budgets
* Non-AWS clusters

🧠 Analogy:
EFS CSI = **AWS provides fridge, power, backup, and repair**

---

## 🔹 Operational Differences (Real Projects)

### NFS CSI (Ops team responsibilities)

* Patch NFS server
* Monitor disk usage
* Handle HA & backup
* Manage performance

### EFS CSI (AWS handles)

* HA across AZs
* Auto scaling storage
* Durability & backup
* Security integration (IAM)

---

## 🔹 Performance Consideration

* **NFS CSI** → performance limited by server size
* **EFS CSI** → scales automatically, better for many Pods

🧠 Analogy:
NFS = **Single water tank**
EFS = **City water supply**

---

## 🔑 Interview One-Line Answers

**Q: NFS CSI vs EFS CSI?**

> “NFS CSI is self-managed and flexible, while EFS CSI is AWS-managed, highly available, and best for production EKS workloads.”

**Q: Which is better for production?**

> “EFS CSI is preferred in AWS production due to built-in HA, scalability, and low ops overhead.”

---

## ⭐ Final Recommendation (Real World)

* 🧪 **Labs / Dev / On-Prem** → NFS CSI
* 🚀 **AWS EKS Production** → EFS CSI

---

![Image](https://www.opcito.com/sites/default/files/inline-images/EFS%20CSI%20Driver%20.png)

![Image](https://www.eksworkshop.com/assets/images/efs-storage-ffc3bb4d0971fa00f17956d14ecd3b71.webp)

![Image](https://docs.aws.amazon.com/images/prescriptive-guidance/latest/patterns/images/pattern-img/2487e285-269b-415b-a270-877f973e3aaf/images/ec8de63c-3307-4010-9e03-2bd7b9881fff.png)

Below is a **FULL, PRODUCTION-READY EFS CSI setup using YAML**, exactly how it’s done in **real AWS EKS projects**, written in **simple English** and **step-by-step**.

---

# 🚀 AWS EFS CSI Driver – Full Setup (Production Way)

## 🟢 Prerequisites (Must Have)

* ✅ Kubernetes cluster on **AWS EKS**
* ✅ **EFS File System** already created
* ✅ EFS mounted in **same VPC** as EKS
* ✅ Worker nodes have **security group access to EFS (2049)**

🧠 **Analogy:**
EFS = **AWS-managed shared hard disk**

---

## 🟢 Step 1: Create IAM Policy for EFS CSI

📄 `efs-csi-policy.json`

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "elasticfilesystem:DescribeAccessPoints",
        "elasticfilesystem:DescribeFileSystems",
        "elasticfilesystem:CreateAccessPoint",
        "elasticfilesystem:DeleteAccessPoint"
      ],
      "Resource": "*"
    }
  ]
}
```

Attach this policy to:

* **Node IAM role**
  (or)
* **IRSA service account** (recommended)

🧠 **Analogy:**
IAM policy = **Permission letter to use storage**

---

## 🟢 Step 2: Install EFS CSI Driver

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-efs-csi-driver/master/deploy/kubernetes/base/kustomization.yaml
```

Verify:

```bash
kubectl get pods -n kube-system | grep efs
```

Expected:

* `efs-csi-controller`
* `efs-csi-node`

🧠 **Analogy:**
CSI Driver = **Translator between Kubernetes and AWS EFS**

---

## 🟢 Step 3: Create StorageClass (Dynamic Provisioning)

📄 `efs-storageclass.yaml`

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: efs-sc
provisioner: efs.csi.aws.com
parameters:
  provisioningMode: efs-ap
  fileSystemId: fs-0abcd1234ef567890   # YOUR EFS ID
  directoryPerms: "700"
reclaimPolicy: Retain
volumeBindingMode: Immediate
```

Apply:

```bash
kubectl apply -f efs-storageclass.yaml
```

🧠 **Analogy:**
StorageClass = **Rules on how AWS should create folders**

---

## 🟢 Step 4: Create PersistentVolumeClaim (PVC)

📄 `efs-pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: efs-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: efs-sc
  resources:
    requests:
      storage: 5Gi
```

Apply:

```bash
kubectl apply -f efs-pvc.yaml
```

Check:

```bash
kubectl get pvc
kubectl get pv
```

✅ PV is **auto-created** by EFS CSI

🧠 **Analogy:**
PVC = **Requesting a shared AWS folder**

---

## 🟢 Step 5: Use EFS PVC in Deployment

📄 `nginx-efs-deploy.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-efs
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-efs
  template:
    metadata:
      labels:
        app: nginx-efs
    spec:
      containers:
        - name: nginx
          image: nginx
          volumeMounts:
            - name: efs-volume
              mountPath: /usr/share/nginx/html
      volumes:
        - name: efs-volume
          persistentVolumeClaim:
            claimName: efs-pvc
```

Apply:

```bash
kubectl apply -f nginx-efs-deploy.yaml
```

🧠 **Analogy:**
Multiple Pods = **Multiple laptops using same Google Drive**

---

## 🟢 Step 6: Verification

```bash
kubectl get pods
kubectl exec -it <pod-name> -- df -h
```

* Write a file in one Pod
* Read it from another Pod
  ✅ Data shared successfully

---

## 🔐 Production Best Practices (IMPORTANT)

✅ Use **IRSA** instead of node IAM
✅ Use **StatefulSet** for DB workloads
✅ Keep **Retain** reclaim policy
✅ Secure EFS with **SG + NACL**
✅ Monitor EFS throughput & latency

---

## ❌ Common Mistakes

❌ Missing port **2049** in security group
❌ Wrong **fileSystemId**
❌ Using EBS when RWX is required
❌ Forgetting IAM permissions

---

## 🔑 Interview-Ready One-Liner

> “In AWS EKS production, we use the EFS CSI driver with a StorageClass to dynamically provision RWX volumes backed by AWS-managed EFS.”

---

## ⭐ Interview Tip

If asked **“Why EFS CSI?”**, say:

> “EFS CSI provides shared, highly available, multi-AZ storage with zero maintenance.”

---
