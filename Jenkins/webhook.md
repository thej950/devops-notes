# 🔔 What is a **Webhook** in Jenkins?

---

## 📌 Simple Meaning

A **Webhook** is a way for **GitHub to automatically notify Jenkins** when something happens, like:

* Code **push**
* New **commit**
* Pull request

When this event happens, GitHub sends an **HTTP POST request** to Jenkins, and **Jenkins immediately starts the pipeline**.

👉 Jenkins does **not** check Git again and again.
👉 GitHub **calls Jenkins only when needed**.

---

## 🧠 Real-Life Analogy

🔔 **Doorbell**

* Visitor presses doorbell
* You immediately open the door
* You don’t keep checking the door every minute

👉 Same way:

* GitHub presses the doorbell (Webhook)
* Jenkins immediately starts the build

---

## 🔄 How Webhook Works (Simple Flow)

1️⃣ Developer pushes code to GitHub
2️⃣ GitHub sends HTTP POST request to Jenkins
3️⃣ Jenkins receives notification
4️⃣ Jenkins triggers CI/CD pipeline immediately

---

## 🚀 Key Features of Webhook

✔ Immediate pipeline trigger
✔ No repeated checking
✔ Faster than Poll SCM
✔ Less load on Jenkins & GitHub
✔ Recommended for real projects

---

## ⚙️ Steps to Configure Webhook (Step-by-Step)

---

### 🔹 Step 1: Configure Webhook in GitHub

1️⃣ Go to **GitHub**
2️⃣ Open your project (example: `maven`)
3️⃣ Click **Settings**
4️⃣ Click **Webhooks**
5️⃣ Click **Add webhook**

---

### 🔹 Step 2: Add Webhook Details

**Payload URL**

```
http://<jenkins_public_ip>:8080/github-webhook/
```

**Content type**

```
application/json
```

👉 Click **Add webhook**

🧠 Analogy
📞 GitHub now knows Jenkins phone number

---

### 🔹 Step 3: Configure Jenkins Job

1️⃣ Open **Jenkins Dashboard**
2️⃣ Select your project
3️⃣ Click **Configure**
4️⃣ Go to **Build Triggers**
5️⃣ Select:

```
GitHub hook trigger for GITScm polling
```

6️⃣ Click **Save**

---

## 🔄 What Happens After Webhook Setup?

* Jenkins **will NOT poll** GitHub
* GitHub sends notification **only when code changes**
* Jenkins triggers **CI and CD automatically**

🧠 Analogy
🚦 Traffic light changes only when needed, not always blinking

---

## 🔐 Authentication / Security

* GitHub uses **secret token** (optional but recommended)
* Jenkins validates incoming request
* Prevents unauthorized triggers

🧠 Analogy
🔐 Doorbell with access control

---

## 🔁 Webhook vs Poll SCM (Very Simple)

| Feature              | Webhook     | Poll SCM   |
| -------------------- | ----------- | ---------- |
| Trigger type         | Event-based | Time-based |
| Speed                | Immediate   | Delayed    |
| Load                 | Low         | High       |
| Jenkins checking Git | ❌ No        | ✅ Yes      |
| Recommended          | ✅ Yes       | ❌ Not much |

---

## 🧪 When Do Real Projects Use Webhook?

✅ All modern CI/CD pipelines
✅ High-traffic repositories
✅ Production-level systems
❌ Rarely avoided unless firewall/network issue

---

## ✅ One-Line Summary

**Webhook means GitHub automatically informs Jenkins when code changes, and Jenkins immediately starts the pipeline — just like pressing a doorbell instead of knocking repeatedly.** 🔔🚀

---
