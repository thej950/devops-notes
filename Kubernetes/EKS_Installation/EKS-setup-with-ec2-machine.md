# ⭐ **Kubernetes EKS Setup using in aws ec2 machine**

# ✅ **1. Launch EC2 Machine (eksServer)**

1. Go to EC2 → Launch Instance
2. Name it: **eksServer**
3. Choose OS → Amazon Linux 2
4. Instance type (t2.medium recommended)
5. Launch the instance

---

# ✅ **2. Create IAM Role with AdministratorAccess**

### **Steps**

1. Go to **IAM → Roles → Create Role**
2. Select **AWS Service**
3. Use case: **EC2**
4. Click **Next**
5. Search for **AdministratorAccess** → Select it
6. Click **Next → Create Role**

### **Attach role to EC2**

1. Go to **EC2 Dashboard**
2. Select **eksServer**
3. Click **Actions → Security → Modify IAM Role**
4. Select your role (example: `kops9`)
5. Attach it

---

# ✅ **3. Install kubectl (on eksServer)**

Open AWS documentation:
[https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)

### **Commands**

```bash
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.25.6/2023-01-30/bin/linux/amd64/kubectl
chmod +x ./kubectl
mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin
kubectl version --short --client
```

✔ This verifies kubectl installation.

---

# ✅ **4. Install eksctl (on eksServer)**

Open AWS documentation:
[https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html)

### **Commands**

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

✔ This verifies eksctl installation.

---

# ✅ **5. Create EKS Cluster**

```bash
eksctl create cluster \
  --name thej-cluster \
  --node-type t2.large \
  --nodes 3 \
  --region ap-southeast-1
```

📌 This command creates:
✔ EKS control plane
✔ 3 worker nodes (t2.large)
✔ Region: Singapore (ap-southeast-1)

⏳ Takes 15–20 minutes.

---

# ✅ **6. Test Cluster Connection**

```bash
kubectl get nodes
```

### ❌ First-time error (expected):

```
Unable to connect to the server: getting credentials: exec: executable aws-iam-authenticator not found

It looks like you are trying to use a client-go credential plugin that is not installed.

https://kubernetes.io/docs/reference/access-authn-authz/authentication/#client-go-credential-plugins
```

This means **aws-iam-authenticator plugin is missing**.

---

# ✅ **7. Install AWS IAM Authenticator**

Open documentation:
[https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html](https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html)

### Install commands:

```bash
curl -Lo aws-iam-authenticator https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.5.9/aws-iam-authenticator_0.5.9_linux_amd64
chmod +x ./aws-iam-authenticator
mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin
echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
aws-iam-authenticator help
```

### Now try:

```bash
kubectl get nodes
```

✔ This time you should see all 3 worker nodes in **Ready** state.

---

# ✅ **8. Delete EKS Cluster**

```bash
eksctl delete cluster thej-cluster --region ap-southeast-1
```

⏳ Takes a few minutes.

---

# 🎯 **Final Clean Summary**

| Step | Description                                              |
| ---- | -------------------------------------------------------- |
| 1    | Launch EC2 (eksServer)                                   |
| 2    | Create IAM role with AdministratorAccess & attach to EC2 |
| 3    | Install kubectl                                          |
| 4    | Install eksctl                                           |
| 5    | Create EKS Cluster                                       |
| 6    | Run kubectl get nodes (fix IAM authenticator error)      |
| 7    | Install aws-iam-authenticator                            |
| 8    | Delete cluster when not needed                           |

---
