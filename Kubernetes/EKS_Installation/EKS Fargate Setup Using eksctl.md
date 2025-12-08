# ⭐ **EKS Fargate Setup Using eksctl (Best Version)**

---

# 🟦 **1. Prerequisites**

Before creating an EKS Fargate cluster, install the following tools:

* **eksctl**
* **kubectl**
* **AWS CLI**

---

# 🟦 **2. Configure AWS Credentials**

You must configure **Access Key** and **Secret Key**.
Use a **root account** or an **IAM user with EKS permissions**.

```bash
aws configure
```

### **Sample Output**

```
AWS Access Key ID [None]: AKIA2LW3XXCQXISFFGWM
AWS Secret Access Key [None]: SPkfJBU8Cb8To8GiI7VjefLXST+x1gikugJ0GGpx
Default region name [None]: us-east-1
Default output format [None]: json
```

✔ This saves credentials under `~/.aws/credentials`.

---

# 🟦 **3. Create EKS Fargate Cluster**

Use eksctl’s built-in Fargate support:

```bash
eksctl create cluster --name thej-cluster-1 --region us-east-1 --fargate
```

### **What this command does**

* Creates EKS control plane
* Configures VPC, subnets, IAM roles automatically
* Creates **default Fargate profile**
* Schedules CoreDNS on Fargate
* Writes kubeconfig to `~/.kube/config`

⏳ Takes ~15 minutes to complete.

---

# 🟦 **4. Sample Output (Important Lines)**

```
using region us-east-1
creating EKS cluster "thej-cluster" in "us-east-1" with Fargate profile
deploying CloudFormation stack "eksctl-thej-cluster-cluster"
creating Fargate profile "fp-default"
"coredns" pods are now scheduled onto Fargate
saved kubeconfig as "/home/vagrant/.kube/config"
EKS cluster "thej-cluster" in "us-east-1" region is ready
```

✔ Cluster is successfully created
✔ CoreDNS is running on Fargate
✔ kubeconfig is saved

---

# 🟦 **5. Connect to the Cluster (from any machine)**

Run this command only when connecting from a **new system**:

```bash
aws eks update-kubeconfig --name thej-cluster-1 --region us-east-1
```

This writes cluster details to:

```
~/.kube/config
```

Now your local kubectl client can access the remote EKS cluster.

---

# 🟦 **6. Verify Cluster Nodes**

In Fargate, you will **not** see EC2 worker nodes.
Instead, Fargate shows **virtual nodes** managed by AWS.

```bash
kubectl get nodes
```

### **Sample Output**

```
NAME                                      STATUS   ROLES    AGE   VERSION
fargate-ip-192-168-123-226.ec2.internal   Ready    <none>   44m   v1.27.7-eks
fargate-ip-192-168-87-72.ec2.internal     Ready    <none>   44m   v1.27.7-eks
```

✔ These nodes are **Fargate compute instances**
✔ No EC2 nodes are created
✔ Pods will run on Fargate automatically

---

# ⭐ **Fargate Cluster Creation Completed Successfully**

---

# 🟥 **(Optional) Deleting the Fargate Cluster**

```bash
eksctl delete cluster --name thej-cluster-1 --region us-east-1
```

This removes:

* EKS Control Plane
* Fargate profiles
* CloudFormation stacks
* VPC resources

---

# 🎯 **Clean Summary (Useful for Revision)**

| Step | Description                                     |
| ---- | ----------------------------------------------- |
| 1    | Install eksctl, kubectl, AWS CLI                |
| 2    | Configure AWS access using `aws configure`      |
| 3    | Create Fargate EKS Cluster using eksctl         |
| 4    | Cluster auto-creates VPC, IAM, Fargate profiles |
| 5    | Update kubeconfig on your machine               |
| 6    | Verify nodes (Fargate virtual nodes)            |
| 7    | Cluster is ready to deploy applications         |

---
