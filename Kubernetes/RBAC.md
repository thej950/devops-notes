# **RBAC (Role-Based Access Control)**

* RBAC is used by Kubernetes to **regulate access to resources** in a cluster
* It helps to **manage and maintain permissions** like read, modify, and admin for different parts of the cluster
* RBAC allows assigning permissions to **specific users, groups, or service accounts**, so they can perform only specific tasks
* It improves **security** and reduces **unauthorized operations** in the cluster
* Using RBAC, we can follow the **principle of least privilege**, where access is given only for minimum required tasks

---

## **Key Components**

* Role, ClusterRole
* RoleBinding, ClusterRoleBinding
* User, Group, ServiceAccount

---

## **Role / ClusterRole**

* **Role**:

  * A Role contains a set of permissions (get, list, create, update, delete)
  * It works **within a specific namespace**

* **ClusterRole**:

  * A ClusterRole contains permissions across the **entire cluster**
  * It works **across all namespaces** or for cluster-level resources

---

## **RoleBinding / ClusterRoleBinding**

* **RoleBinding**:

  * Associates a **Role** with a user, group, or service account
  * Applies **only to a specific namespace**

* **ClusterRoleBinding**:

  * Associates a **ClusterRole** with a user, group, or service account
  * Applies **across the entire cluster**

---

## **User, Group, ServiceAccount**

* **User**:

  * A single human user accessing the cluster

* **Group**:

  * A collection of users

* **ServiceAccount**:

  * An account used by **applications or pods** inside the cluster

---

## **How RBAC Works**

* RBAC works by **binding roles** to users, groups, or service accounts
* This is done using **RoleBinding or ClusterRoleBinding**
* The bound subject can perform **only the allowed actions**

---

## **Examples**

* A Role may allow a user to **only view pods** in a specific namespace
* A ClusterRole may give an admin **full access** to all resources in the cluster

---

# Anology For RBAC
Here is a **simple, easy-to-remember analogy** for **RBAC**, using the **same points you studied** 👇

---

## **RBAC Analogy (Office / Company Example)**

### 🏢 **Kubernetes Cluster = Company Building**

* The **Kubernetes cluster** is like a **company building**
* Different **teams and rooms** are like **namespaces**
* Resources (pods, services, secrets) are like **files, systems, and rooms**

---

### 👤 **User / Group / ServiceAccount**

* **User** → Employee (single person)
* **Group** → Team (Dev, QA, Ops)
* **ServiceAccount** → Application or system user (like a CI/CD bot)

---

### 📄 **Role**

* A **Role** is like a **job description**
* It defines **what actions** are allowed (read, write, delete)
* Valid **only in one department (namespace)**

👉 Example:
“QA can only **view reports** in QA department”

---

### 🌍 **ClusterRole**

* A **ClusterRole** is like a **company-wide job role**
* Permissions apply to **all departments**
* Used for admins or security teams

👉 Example:
“IT Admin can access **everything in the company**”

---

### 🔗 **RoleBinding**

* RoleBinding is like **assigning a job role to an employee in one department**
* Works **only for that department**

👉 Example:
“Assign QA role to Rahul in QA department”

---

### 🔗 **ClusterRoleBinding**

* ClusterRoleBinding is like **assigning a company-wide role**
* Works **across the whole company**

👉 Example:
“Assign IT Admin role to Suresh for all departments”

---

### 🔐 **Least Privilege**

* Give **only required access**, not extra
* Improves security and avoids mistakes

👉 Example:
“Intern gets view-only access, not delete access”

---

### 🎯 **One-line Memory Trick**

> **Role = What you can do**
> **Binding = Who can do it & where**

---

# **RBAC Best Practices**

### **1. Principle of Least Privilege**

* Assign **only the minimum access** required
* Avoid broad permissions for users, groups, or service accounts
* Never give admin access unless it is really needed

---

### **2. Namespace Isolation**

* Assign roles **per namespace** to limit access scope
* Use ClusterRole **only when access is required across all namespaces**
* This reduces risk and limits blast radius

---

### **3. Audit Roles and RoleBindings**

* Regularly review **Roles and RoleBindings**
* Remove unused or outdated permissions
* Ensure permissions match **current security policies**

---

### **4. Use ServiceAccounts for Applications**

* Assign permissions to **ServiceAccounts**, not human users
* Each application should have its **own ServiceAccount**
* This ensures **scoped and controlled access** per application

---

## **Simple Memory Line**

> **Least access + Namespace control + Regular review + ServiceAccounts**

---

### ✅ **Interview Tip**

👉 Say this confidently:
**“In production, we follow least privilege, namespace isolation, and use ServiceAccounts instead of users.”**




# Practicals 
---

## **Step 1: Create `dev` namespace**

**File:** `01-namespace.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

---

## **Step 2: Create Role (read-only access to pods)**

**File:** `02-role-pod-readonly.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-read-only
  namespace: dev
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

👉 This Role allows **view pods only** in `dev` namespace.

---

## **Step 3: Create RoleBinding for user `thej`**

**File:** `03-rolebinding-thej.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: thej-pod-readonly-binding
  namespace: dev
subjects:
- kind: User
  name: thej
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-read-only
  apiGroup: rbac.authorization.k8s.io
```

👉 This binds the Role to **user `thej`** in `dev` namespace.

---

## **Apply all files**

```bash
kubectl apply -f 01-namespace.yaml
kubectl apply -f 02-role-pod-readonly.yaml
kubectl apply -f 03-rolebinding-thej.yaml
```

---

## **Verification (important for interview)**

```bash
kubectl auth can-i get pods --namespace=dev --as=thej
kubectl auth can-i delete pods --namespace=dev --as=thej
```

✅ First command → **yes**
❌ Second command → **no**

---

## **Important Note (Real-time clarity)**

* Kubernetes does **not create users internally**
* User `thej` must exist in **kubeconfig / external auth** (cert, IAM, OIDC)
* RBAC only **controls permissions**, not user creation

---

### ✅ **Interview Tip**

👉 Say this line confidently:
**“Role defines permissions, RoleBinding assigns those permissions to a user in a namespace.”**

## **Real Scenario**

* IAM user **`thej`** is created in **Amazon Web Services**
* Kubernetes cluster is **Amazon EKS**
* Goal:
  👉 `thej` can **only view pods** in `dev` namespace

---

## **Step 1: Create IAM user `thej` (AWS side)**

* Go to AWS IAM → Users → Create user
* Name: `thej`
* Access type: **Programmatic access**
* Attach policy: `AmazonEKSReadOnlyAccess` (for learning)

👉 IAM user is **authentication source**

---

## **Step 2: Map IAM user to Kubernetes (IMPORTANT)**

Kubernetes must **recognize** the IAM user.

Edit **aws-auth ConfigMap** in EKS:

```bash
kubectl edit configmap aws-auth -n kube-system
```

Add this under `mapUsers`:

```yaml
mapUsers: |
  - userarn: arn:aws:iam::<ACCOUNT-ID>:user/thej
    username: thej
    groups:
      - dev-readers
```

👉 Now EKS knows:

* IAM user → `thej`
* Kubernetes username → `thej`

---

## **Step 3: Create `dev` namespace**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

---

## **Step 4: Create Role (read-only pods)**

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-read-only
  namespace: dev
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

---

## **Step 5: Bind Role to IAM user `thej`**

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: thej-readonly-binding
  namespace: dev
subjects:
- kind: User
  name: thej
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-read-only
  apiGroup: rbac.authorization.k8s.io
```

---

## **Step 6: Test access (VERY IMPORTANT)**

```bash
kubectl auth can-i get pods -n dev --as=thej
kubectl auth can-i delete pods -n dev --as=thej
```

✅ get pods → **yes**
❌ delete pods → **no**

---

## **Flow (Easy to remember)**

```
IAM User (thej)
   ↓
aws-auth ConfigMap
   ↓
Kubernetes User (thej)
   ↓
Role + RoleBinding
   ↓
Allowed actions only
```

---


---

### ✅ **Interview Tip**

👉 Say this confidently:
**“In EKS, IAM handles authentication and Kubernetes RBAC handles authorization using aws-auth ConfigMap.”**


## For Service Accounts 
---

## **Step 1: Create `dev` namespace**

**File:** `01-namespace.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

---

## **Step 2: Create ServiceAccount**

**File:** `02-serviceaccount.yaml`

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: thej-sa
  namespace: dev
```

👉 This ServiceAccount represents an **application identity**, not a human user.

---

## **Step 3: Create Role (read-only access to pods)**

**File:** `03-role-pod-readonly.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-read-only
  namespace: dev
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

👉 This Role allows **view-only access to pods** in `dev`.

---

## **Step 4: Create RoleBinding for ServiceAccount**

**File:** `04-rolebinding-thej-sa.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: thej-sa-pod-readonly-binding
  namespace: dev
subjects:
- kind: ServiceAccount
  name: thej-sa
  namespace: dev
roleRef:
  kind: Role
  name: pod-read-only
  apiGroup: rbac.authorization.k8s.io
```

👉 This binds the Role to **ServiceAccount `thej-sa`** in `dev`.

---

## **Step 5: Use ServiceAccount in a Pod**

**File:** `05-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
  namespace: dev
spec:
  serviceAccountName: thej-sa
  containers:
  - name: nginx
    image: nginx
```

👉 Any pod using this ServiceAccount gets **read-only pod access**.

---

## **Apply all files**

```bash
kubectl apply -f 01-namespace.yaml
kubectl apply -f 02-serviceaccount.yaml
kubectl apply -f 03-role-pod-readonly.yaml
kubectl apply -f 04-rolebinding-thej-sa.yaml
kubectl apply -f 05-pod.yaml
```

---

## **Verification (important for interview)**

```bash
kubectl auth can-i get pods -n dev \
--as=system:serviceaccount:dev:thej-sa

kubectl auth can-i delete pods -n dev \
--as=system:serviceaccount:dev:thej-sa
```

✅ First command → **yes**
❌ Second command → **no**

---

## **Important Note (Real-time clarity)**

* ServiceAccounts are **created inside Kubernetes**
* Mostly used by **applications, Jenkins, controllers**
* Preferred over users for **production workloads**

---

### ✅ **Interview Tip**

👉 Say this confidently:
**“For applications, we use ServiceAccounts with RBAC instead of human users.”**

## ClusterRole for user and Service Account 
---

## ✅ **SETUP–1: ClusterRole for USER `thej`**

### 🎯 Goal

User `thej` can **only view pods in `dev` namespace**

---

## **Step 1: Create ClusterRole**

**File:** `thej-clusterrole.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: thej-pod-readonly
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

---

## **Step 2: Bind ClusterRole to USER `thej`**

**File:** `thej-clusterrolebinding.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: thej-pod-readonly-binding
subjects:
- kind: User
  name: thej
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: thej-pod-readonly
  apiGroup: rbac.authorization.k8s.io
```

---

## **Verify**

```bash
kubectl auth can-i get pods -n dev --as=thej
kubectl auth can-i delete pods -n dev --as=thej
```

✔ get pods → **YES**
❌ delete pods → **NO**

---

### 🔑 Important (User case)

* User `thej` comes from **IAM / kubeconfig / SSO**
* Kubernetes **does NOT create users**

---

## ✅ **SETUP–2: ClusterRole for JENKINS ServiceAccount**

### 🎯 Goal

Jenkins app can **only view pods in `dev` namespace**

---

## **Step 1: Create ServiceAccount**

**File:** `jenkins-sa.yaml`

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: dev
```

---

## **Step 2: Create ClusterRole**

**File:** `jenkins-clusterrole.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: jenkins-pod-readonly
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

---

## **Step 3: Bind ClusterRole to ServiceAccount**

**File:** `jenkins-clusterrolebinding.yaml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: jenkins-pod-readonly-binding
subjects:
- kind: ServiceAccount
  name: jenkins
  namespace: dev
roleRef:
  kind: ClusterRole
  name: jenkins-pod-readonly
  apiGroup: rbac.authorization.k8s.io
```

---

## **Verify**

```bash
kubectl auth can-i get pods -n dev \
--as=system:serviceaccount:dev:jenkins

kubectl auth can-i delete pods -n dev \
--as=system:serviceaccount:dev:jenkins
```

✔ get pods → **YES**
❌ delete pods → **NO**

---

## 🧠 **ONE-LINE MEMORY TRICK**

```
User  → ClusterRole + ClusterRoleBinding
App   → ServiceAccount + ClusterRole + ClusterRoleBinding
```

---

### ✅ **INTERVIEW TIP**

👉 Say this confidently:
**“Users are external, ServiceAccounts are internal, but both use ClusterRole and ClusterRoleBinding for permissions.”**

## **Important Real-Time Notes**

* **ClusterRole** → defines permissions cluster-wide
* **ClusterRoleBinding** → attaches permissions globally
* Namespace restriction is enforced **when accessing resources**
* Best practice:

  * **Users** → ClusterRole (view/admin)
  * **CI/CD (Jenkins)** → ServiceAccount + ClusterRole

---
 


# Some Commands 

```bash
k get roles -n dev # to list roles in dev namespace
k get rolebindings -n dev # to list rolebindings in dev namespace
k get clusterroles # to list clusterroles
k get clusterrolebindings # to list clusterrolebindings 
k describe role pod-read-only -n dev # Describe a specific role
k auth can-i get pods -n dev --as=thej # Check who has access (very important)


```
