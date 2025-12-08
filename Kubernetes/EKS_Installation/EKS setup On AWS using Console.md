# ⭐ **EKS Setup on AWS Using Console – Complete Guide**

---

# 🟦 **Introduction**

* **Kubernetes** is a container orchestration and management tool.
* On AWS, Kubernetes clusters can be set up using three services:

### **Ways to run Kubernetes on AWS**

1. **ECS** (Elastic Container Service)
2. **EKS** (Elastic Kubernetes Service)
3. **Fargate** (Serverless Kubernetes pod execution)

---

# 🟦 **Kubernetes Installation Options**

### **For Development**

* Minikube
* Docker Desktop (built-in Kubernetes)

### **Unmanaged Kubernetes (installer-based)**

* kubeadm
* kops
* Kubespray

### **Managed Kubernetes Services**

* AKS → Azure Kubernetes Service
* EKS → Elastic Kubernetes Service (AWS)
* GKE → Google Kubernetes Engine
* OKE → Oracle Kubernetes Engine

---

# 🟦 **EKS Setup Methods**

1. **Using eksctl (CLI-based setup)**
2. **Using AWS Console (UI-based setup)** → *We are using this method*

---

# ⭐ **EKS Setup Using AWS Console**

---

# 🟦 **Step 1: Create EKS Cluster via Console**

### 1. Go to AWS Console → Search **EKS**

### 2. Click **Create Cluster**

---

## **Step 1: Configure Cluster**

* **Cluster name:** `thej-cluster`
* **Kubernetes version:** `1.28`
* **Cluster service role:** Automatically created by AWS
* **Cluster Access:**

  * Bootstrap administrator access → **Allow administrator access**
  * Authentication mode → **EKS API and ConfigMap**

---

## **Step 2: Specify Networking**

* **VPC:** Select **default VPC**
* **Subnets:** Select **all subnets**
* **Security groups:** Leave default (AWS creates automatically)
* **Cluster endpoint:**

  * Select **Public and Private**

---

## **Step 3: Configure Observability**

Enable the following logs:

* API Server
* Authenticator
* Controller Manager
* Scheduler

---

## **Step 4: Select Add-ons**

Keep default add-ons:

* kube-proxy
* CoreDNS
* Amazon VPC CNI
* Amazon EKS Pod Identity Agent

---

## **Step 5 & 6: Review and Create**

Click **Create**
⏳ Cluster takes ~10 minutes to create.

---

# 🟦 **Step 2: Install AWS CLI & Kubectl (on Windows)**

### Check cluster status:

```powershell
aws eks --region ap-northeast-1 describe-cluster --name the-cluster-3 --query cluster.status
```

Expected output:

```
"ACTIVE"
```

### Configure Kubectl:

```powershell
aws eks --region ap-northeast-1 update-kubeconfig --name the-cluster-3
```

### Test connection:

```powershell
kubectl get svc
```

---

# 🟦 **Step 3: Create Worker Node Group**

Workers require an EC2 IAM Role.

---

## **Create IAM Role for Worker Nodes**

### IAM → Roles → Create Role

* **Trusted entity:** AWS Service
* **Use case:** EC2

### Add these policies:

* `AmazonEKSWorkerNodePolicy`
* `AmazonEKS_CNI_Policy`
* `AmazonEC2ContainerRegistryReadOnly`

### Role name:

`eks-cluster-worker_node_group1_role`

---

# 🟦 **Create Node Group in your EKS Cluster**

Go to:
**EKS → thej-cluster → Compute → Add Node Group**

---

## **Step 1: Configure Node Group**

* **Node group name:** `group1`
* **IAM role:** Select `eks-cluster-worker_node_group1_role`

---

## **Step 2: Compute & Scaling**

### Compute:

* AMI type → Amazon Linux
* Capacity type → On-Demand
* Instance type → `t3.micro`
* Disk size → 20 GB

### Scaling:

* Desired size → 2
* Minimum size → 2
* Maximum size → 2

---

## **Step 3: Networking**

* Subnets → Select all
* Enable Remote Access → Yes

  * Choose Key Pair (`.pem` file)
  * Allow remote access from → Anywhere (0.0.0.0/0)

---

## **Step 4: Review & Create**

Node group launches in ~5 minutes.

---

# 🟦 **Step 4: Verify Worker Nodes**

```powershell
kubectl get nodes
```

Expected output:

```
NAME                                               STATUS   ROLES    AGE   VERSION
ip-172-31-25-69.ap-northeast-1.compute.internal    Ready    <none>   51s   v1.28.5-eks
ip-172-31-34-103.ap-northeast-1.compute.internal   Ready    <none>   53s   v1.28.5-eks
```

✔ Your EKS cluster is ready.
✔ Worker nodes successfully joined.

---

# 🟥 **Deleting the EKS Cluster (Cleanup)**

**Step 1: Delete Node Group**
Go to EKS Console → Cluster → Compute → Delete Node Group

**Step 2: Delete Cluster**

```bash
eksctl delete cluster thej-cluster
```

Or delete via AWS Console → EKS → Delete Cluster

---

# 🎯 **Clean Summary (Best for Interview Revision)**

| Step | Task                                        |
| ---- | ------------------------------------------- |
| 1    | Create EKS Cluster (Console)                |
| 2    | Install AWS CLI, kubectl                    |
| 3    | Configure kubeconfig                        |
| 4    | Create IAM Role for worker nodes            |
| 5    | Create Node Group                           |
| 6    | Verify nodes with kubectl                   |
| 7    | Cleanup (delete nodegroup → delete cluster) |

---
