# ⭐ **Opening Ports in EKS**

In Amazon EKS, worker nodes run inside EC2 instances/VPC, so you must update the **EC2 Security Group** to allow external traffic.

---

# 🟦 **1. Open an Inbound Port in EKS Node Security Group**

Use this command to allow traffic from the internet:

```bash
aws ec2 authorize-security-group-ingress \
  --group-id sg-0f9eee68d63570cfb \
  --region ap-south-1 \
  --protocol tcp \
  --port 8080 \
  --cidr 0.0.0.0/0
```

✔ Opens port **8080**
✔ Allows access from **anywhere**
✔ Applies to **worker node security group**

---

# 🟦 **Common Security Group Commands**

### ✅ **Allow inbound traffic**

```bash
aws ec2 authorize-security-group-ingress
```

### ✅ **Allow outbound traffic**

```bash
aws ec2 authorize-security-group-egress
```

### ❌ **Remove inbound rule**

```bash
aws ec2 revoke-security-group-ingress
```

---

# 🟦 **Quick Explanation (Interview Style)**

* **Ingress → Incoming traffic**
* **Egress → Outgoing traffic**
* **Revoke → Remove access rule**

---

# 🎯 Best Practice

Use **Ingress** rules to expose applications on EKS through **Load Balancers**.
Avoid opening ports directly on worker nodes unless for testing.

---
