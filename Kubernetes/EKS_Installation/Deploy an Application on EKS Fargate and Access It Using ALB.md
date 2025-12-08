# ⭐ **Deploy an Application on EKS Fargate and Access It Using ALB (Best Version)**

---

# 🟦 **Step 1: Create EKS Fargate Cluster**

Create a Fargate-enabled EKS cluster in **us-east-1**:

```bash
eksctl create cluster --name thej-cluster-1 --region us-east-1 --fargate
```

⏳ Takes 10–15 minutes.
✔ Default Fargate profile created
✔ CoreDNS runs on Fargate

---

# 🟦 **Step 2: Create a Custom Fargate Profile**

Create a Fargate profile for the namespace **game-2048**:

```bash
eksctl create fargateprofile \
--cluster thej-cluster-1 \
--region us-east-1 \
--name alb-sample-app \
--namespace game-2048
```

✔ This ensures workloads in `game-2048` run on Fargate.
⏳ Takes 3–5 minutes.

---

# 🟦 **Step 3: Deploy Sample Application (Game 2048)**

Use AWS-provided YAML to deploy:

* Namespace
* Deployment
* Service
* Ingress

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

### Verify Pods:

```bash
kubectl get pods -n game-2048
```

### Verify Service:

```bash
kubectl get svc -n game-2048
```

### Verify Ingress:

```bash
kubectl get ingress -n game-2048
```

⚠️ At this point, **Ingress has no external address**, because ALB Controller is not installed yet.

---

# 🟦 **Step 4: Associate IAM OIDC Provider**

ALB controller requires IAM OIDC:

```bash
eksctl utils associate-iam-oidc-provider \
--cluster thej-cluster-1 \
--approve
```

✔ Creates IAM OIDC provider for service accounts.

---

# 🟦 **Step 5: Install AWS Load Balancer Controller**

The ALB Ingress controller is required for external access.

---

## **5.1 Download IAM Policy**

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
```

## **5.2 Create IAM Policy**

```bash
aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://iam_policy.json
```

✔ Policy created successfully.

---

## **5.3 Create Service Account with IAM Role**

```bash
eksctl create iamserviceaccount \
--cluster=thej-cluster-1 \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--role-name AmazonEKSLoadBalancerControllerRole \
--attach-policy-arn=arn:aws:iam::<your-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
--approve
```

---

## **5.4 Install AWS Load Balancer Controller Using Helm**

### Add Helm repo:

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks
```

### Install ALB controller:

```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
-n kube-system \
--set clusterName=thej-cluster-1 \
--set serviceAccount.create=false \
--set serviceAccount.name=aws-load-balancer-controller \
--set region=us-east-1 \
--set vpcId=vpc-027d5d0df04bd8b8d
```

✔ ALB controller deployed
✔ Runs inside kube-system namespace

---

## **5.5 Verify ALB Controller**

### Check deployment:

```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```

### Check pods:

```bash
kubectl get pods -n kube-system -w
```

---

# 🟦 **Step 6: Verify Application Access (ALB Created)**

Check ingress again:

```bash
kubectl get ingress -n game-2048
```

Output example:

```
NAME           CLASS   HOSTS   ADDRESS                                                                 PORTS   AGE
ingress-2048   alb     *       k8s-game2048-ingress2-83f5e653cf-295140272.us-east-1.elb.amazonaws.com   80      48m
```

Copy the **ADDRESS** value → open in browser.

✔ Game 2048 opens
✔ Application running on Fargate
✔ ALB routes external traffic to pods

---

# 🟥 **Step 7: Cleanup / Delete Fargate Cluster**

---

## **7.1 Delete Application**

```bash
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

Delete pods only (optional):

```bash
kubectl delete pods -l app=2048-game
```

---

## **7.2 Delete ALB Controller**

Uninstall Helm:

```bash
helm uninstall aws-load-balancer-controller -n kube-system
```

Delete IAM Policy:

```bash
aws iam delete-policy --policy-arn arn:aws:iam::<your-account-id>:policy/AWSLoadBalancerControllerIAMPolicy
```

---

## **7.3 Delete Fargate Profile**

```bash
eksctl delete fargateprofile \
--cluster thej-cluster-1 \
--region us-east-1 \
--name alb-sample-app
```

---

## **7.4 Delete EKS Fargate Cluster**

```bash
eksctl delete cluster --name thej-cluster-1 --region us-east-1
```

✔ Deletes fargate profiles
✔ Deletes cluster
✔ Deletes cloudformation stacks
✔ Deletes networking and IAM roles

---

# 🎯 **Complete Summary (Best for Revision)**

| Step | Description                                             |
| ---- | ------------------------------------------------------- |
| 1    | Create EKS Fargate cluster                              |
| 2    | Create custom Fargate profile                           |
| 3    | Deploy 2048 sample app (deployment + service + ingress) |
| 4    | Create IAM OIDC provider                                |
| 5    | Create IAM Role + install ALB controller using Helm     |
| 6    | Check ingress for ALB URL → Access in browser           |
| 7    | Cleanup resources and delete cluster                    |

---
