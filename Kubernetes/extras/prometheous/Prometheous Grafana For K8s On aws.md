# 📊 Prometheus & Grafana for Kubernetes (EKS) on AWS

*(Beginner-Friendly Guide)*

---

## 🔹 1. Pre-Requisites

Before starting, make sure you have:

* AWS CLI
* eksctl
* kubectl
* Helm (Helm Charts)

These tools help us:

* Create EKS cluster
* Manage Kubernetes
* Install Prometheus & Grafana easily

---

## 🔹 Step 1: Install AWS CLI on Ubuntu

AWS CLI helps your local machine talk to AWS.

```
sudo apt update
sudo apt install awscli -y
```

Verify installation:

```
aws --version
```

---

## 🔹 Step 2: Install eksctl

`eksctl` is a tool to **create and manage EKS clusters easily**.

```
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
```

Verify:

```
eksctl version
```

---

## 🔹 Step 3: Configure AWS Credentials

AWS credentials allow your machine to authenticate with AWS.

Create **Access Key & Secret Key** in AWS IAM.

```
aws configure
```

Enter details:

```
AWS Access Key ID: <YOUR_ACCESS_KEY>
AWS Secret Access Key: <YOUR_SECRET_KEY>
Default region name: <REGION>
Default output format: json
```

---

## 🔹 Step 4: Install kubectl

`kubectl` is used to **manage Kubernetes cluster remotely**.

```
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin
```

Verify:

```
kubectl version --client
```

---

## 🔹 Step 5: Install Helm

Helm helps install complex applications like Prometheus & Grafana **very easily**.

Why Helm?

* No manual YAML headache
* Works out-of-the-box
* Installs Prometheus, Alertmanager, Operator automatically

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

Verify:

```
helm version
```

---

## 🔹 Step 6: Create EKS Cluster using eksctl

```
eksctl create cluster \
--name test-cluster-1 \
--version 1.22 \
--region eu-central-1 \
--nodegroup-name worker-nodes \
--node-type t2.large \
--nodes 2 \
--nodes-min 2 \
--nodes-max 3
```

### Explanation:

* `--name` → Cluster name
* `--version` → Kubernetes version
* `--region` → AWS region
* `--nodegroup-name` → Worker nodes group
* `--node-type` → EC2 instance type
* `--nodes` → Number of nodes
* `--nodes-min/max` → Auto scaling limits

⏳ **Note:** Cluster creation takes **10–15 minutes**

---

## 🔹 Step 7: Install Kubernetes Metrics Server

Metrics Server allows Kubernetes to:

* Collect CPU & Memory metrics
* Enable HPA (Auto Scaling)
* Allow Prometheus to scrape metrics

Install:

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Verify:

```
kubectl get pods -n kube-system
```

---

## 🔹 Step 8: Install Prometheus using Helm

Add Helm repo:

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Create namespace:

```
kubectl create namespace prometheus
```

Install Prometheus:

```
helm install prometheus prometheus-community/prometheus \
--namespace prometheus \
--set alertmanager.persistentVolume.storageClass="gp2" \
--set server.persistentVolume.storageClass="gp2"
```

Verify:

```
kubectl get all -n prometheus
```

Access Prometheus:

```
kubectl port-forward deployment/prometheus-server 9090:9090 -n prometheus
```

Open:

```
http://localhost:9090
```

---

## 🔹 Step 9: Install Grafana

Add Grafana repo:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

### Create Prometheus Datasource file

```
vim prometheus-datasource.yaml
```

```yaml
datasources:
  datasources.yaml:
    apiVersion: 1
    datasources:
    - name: Prometheus
      type: prometheus
      url: http://prometheus-server.prometheus.svc.cluster.local
      access: proxy
      isDefault: true
```

Create namespace:

```
kubectl create namespace grafana
```

Install Grafana:

```
helm install grafana grafana/grafana \
--namespace grafana \
--set persistence.enabled=true \
--set persistence.storageClassName="gp2" \
--set adminPassword='EKS!sAWSome' \
--values prometheus-datasource.yaml \
--set service.type=LoadBalancer
```

Verify:

```
kubectl get all -n grafana
```

Get Grafana URL:

```
kubectl get service -n grafana
```

Login:

* Username: `admin`
* Password: `EKS!sAWSome`

---

## 🔹 Step 10: Import Grafana Dashboard

Use ready-made Kubernetes dashboard.

Dashboard ID: **6417**

Steps:

* Grafana → Dashboard → Import
* Enter **6417**
* Select Prometheus datasource
* Click Import

---

## 🔹 Step 11: Deploy Spring Boot App & Monitor

Create file:

```
vim k8s-spring-boot-deployment.yml
```

(YAML remains **unchanged**, it is correct ✅)

Apply:

```
kubectl apply -f k8s-spring-boot-deployment.yml
```

Verify:

```
kubectl get deployment jhooq-springboot
```

Now you can see:

* Pod CPU usage
* Memory usage
* Requests & limits
* Node performance in Grafana

---

## ✅ Final Architecture (Simple)

* **Metrics Server** → Collects K8s metrics
* **Node Exporter** → Node-level metrics
* **Prometheus** → Scrapes & stores data
* **Grafana** → Visualizes & alerts

---

### 📌 Reference

[https://jhooq.com/prometheous-k8s-aws-setup/](https://jhooq.com/prometheous-k8s-aws-setup/)

---

