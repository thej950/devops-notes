**Helm**

![Image](https://miro.medium.com/0%2AesRQuHf5IdjInG3l.png)

1. Helm is a **package manager for Kubernetes**, like *apt* for Linux.
2. It helps us **install, upgrade, and delete Kubernetes apps easily**.
3. Helm uses **Charts** → a chart is a bundle of YAML files (Deployment, Service, ConfigMap).
4. We use **values.yaml** to change configs (replicas, image, ports) without editing YAMLs.
5. In my real project, we used Helm to deploy apps on Kubernetes instead of managing 10+ YAML files.
6. Helm supports **versioning**, so rollback is easy if deployment fails.
7. Helm works with CI/CD tools like **Jenkins + GitHub Actions** for automated deployments.
8. Common commands: `helm install`, `helm upgrade`, `helm rollback`.

**Interview Tip:**
👉 Say: *“Helm simplifies Kubernetes deployments by packaging manifests and enabling easy upgrades and rollbacks.”*

**What is Helm?**

* Helm is a **package manager for Kubernetes**.
* It helps us **install applications like Nginx, HTTPD, WordPress** easily in Kubernetes.

**Why we use Helm (Problem → Solution):**

* Without Helm, we must create **many YAML files** (pod, service, deployment, ingress).
* With Helm, **one command** can create all required pods and services.
* This saves time and avoids manual errors.

**Easy Analogy (Very Important for Interview):**

* Think **Kubernetes = Mobile Phone**
* **Helm = Play Store**
* **Helm Chart = App (like WhatsApp)**
  👉 Instead of installing APK files one by one, we just click *Install*.

**How Helm Works:**

* Helm uses something called a **Chart**.
* A chart is a **folder with all Kubernetes YAML files**.
* Inside the chart, **values.yaml** is the main file where we change:

  * image name
  * replica count
  * ports
* By default, Helm installs **Nginx settings**, but we customize using `values.yaml`.

**Managed vs Unmanaged Kubernetes:**

* Managed K8s (EKS, AKS, GKE) → Helm usually available.
* Unmanaged K8s → We must **install Helm manually**.

---

### Helm Installation Steps

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

Check Helm version:

```bash
helm version
```

---

### Create a Helm Chart

```bash
helm create mynginx
```

Important files:

* `Chart.yaml` → Chart information
* `values.yaml` → Configuration (most important)
* `templates/` → Kubernetes YAML templates

---

### Install & Delete Helm Chart

```bash
helm install nginx1 mynginx
```

* `nginx1` → release name
* `mynginx` → chart name

Delete:

```bash
helm delete nginx1
```

---

### Helm Repositories

```bash
helm repo add my-repo https://charts.bitnami.com/bitnami
helm repo list
helm repo update
```

Install app from repo:

```bash
helm install my-release my-repo/wordpress
```

👉 This single command creates **WordPress + database pods automatically**.

---

### Interview Tip ⭐

👉 Say: *“Helm reduces Kubernetes YAML complexity by packaging resources into charts and enabling easy install, upgrade, and rollback.”*


## 1️⃣ Creating a Helm Chart

```bash
helm create helloworld
```

* `helm` → main command
* `create` → action
* `helloworld` → **custom chart name** (can be anything)
* This command creates a **ready-made Helm template**
* By default, it uses an **NGINX image**

### Chart structure created:

```
helloworld/
├── Chart.yaml        → chart info (name, version)
├── values.yaml       → configuration file (MOST IMPORTANT)
├── charts/           → sub-charts
└── templates/        → Kubernetes YAML files
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    └── others
```

👉 Think of this as a **starter project** for Kubernetes apps.

---

## 2️⃣ Update values.yaml (Configuration)

Before deployment, we modify `values.yaml`.

```bash
vim helloworld/values.yaml
```

Example change:

```yaml
service:
  type: NodePort
  port: 80
```

* `values.yaml` controls **image, replicas, service type**
* No need to edit multiple YAML files
* One file controls everything

---

## 3️⃣ Install (Deploy) Helm Chart

```bash
helm install myhelloworld helloworld
```

* `myhelloworld` → **release name** (runtime name, can be anything)
* `helloworld` → **chart name**
* This command creates **Pod, Service, Deployment automatically**

📌 Output shows:

* status: deployed
* revision: 1
* commands to access the app

---

## 4️⃣ List Helm Releases

```bash
helm list -a
```

* Shows all Helm deployments
* Displays release name, status, chart version

---

## 5️⃣ Uninstall (Delete) Helm Release

```bash
helm uninstall myhelloworld
```

* Deletes all Kubernetes resources created by Helm
* Clean and safe removal

---

## 6️⃣ Common Helm Commands (Very Important)

```bash
helm create        → create a chart template
helm install       → deploy chart
helm upgrade       → update existing release
helm rollback      → go back to previous version
helm lint          → validate chart
helm template      → render YAML (no API call)
helm --dry-run     → test before real deploy
helm uninstall     → delete release
```

---

## 🔁 Upgrade Example

```bash
helm upgrade myhelloworld helloworld
```

* Applies latest changes in `values.yaml` or templates
* Revision number increases

### Rollback Example

```bash
helm rollback myhelloworld 1
```

* Moves back to **revision 1**

---

## 🧪 Dry Run vs Template

* `helm --dry-run --debug`
  → talks to Kubernetes API (safe test)

* `helm template`
  → only checks local templates (no API)

---

## 🔄 Simple Analogy (Interview Gold ⭐)

* **Kubernetes** = Kitchen
* **YAML files** = Raw ingredients
* **Helm Chart** = Recipe book
* **values.yaml** = Spice level you adjust
* **helm install** = Cooking the dish

👉 One command cooks the full meal 🍽️

---

## ⭐ Interview Tip

Say this confidently:

> “Helm simplifies Kubernetes deployments by using charts and values.yaml, reducing multiple YAML files into one manageable package.”

- **Helm Notes** [[ClickHere](https://drive.google.com/file/d/1xMDCparCy6bGb6Zt0bt0xIOXd8o7RmzH/view?usp=drive_link)]


