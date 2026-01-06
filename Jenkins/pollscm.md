# 🔁 What is **Poll SCM** in Jenkins?

---

## 📌 Simple Meaning

**Poll SCM** means Jenkins **keeps checking the Git repository again and again** at a fixed time interval.

If Jenkins **finds any new commit**, it will **automatically trigger the build**.

👉 Jenkins asks Git:

> “Is there any new code now?”

---

## 🧠 Real-Life Analogy

📬 **Checking a letter box**

* You don’t know when letter will come
* You check the box **every 5 minutes**
* If letter is there → you read it

👉 Poll SCM works the same way:

* Jenkins checks Git again and again
* If new code is found → build starts

---

## 🔍 How Poll SCM Works (Step by Step)

1️⃣ Jenkins checks Git repository
2️⃣ If **no new commit** → do nothing
3️⃣ If **new commit found** → build starts automatically

⚠️ Jenkins keeps checking even if no changes happened

---

## ⏰ Poll SCM Schedule (Cron Format)

You define **how often Jenkins should check Git** using cron syntax.

### Example:

```
H/5 * * * *
```

### Meaning:

* Jenkins checks Git **every 5 minutes**

---

### Cron Fields (Simple Reminder)

```
MINUTE   HOUR   DAY   MONTH   DAY-OF-WEEK
```

Example:

```
* * * * *
```

🧠 Meaning:

* Check every minute
* Every hour
* Every day

---

## ⭐ What does `*` (Star) mean?

### 📌 Simple Meaning

`*` means **repeat / every time**

### Example:

```
* * * * *
```

🧠 Analogy:
⏰ Alarm ringing **every minute**

---

## ⚙️ Steps to Configure Poll SCM in Jenkins

1️⃣ Open **Jenkins Job**
2️⃣ Click **Configure**
3️⃣ Go to **Build Triggers**
4️⃣ Select **Poll SCM**
5️⃣ Enter cron schedule (example below)

```
H/5 * * * *
```

6️⃣ Save the job

👉 Jenkins will now check Git automatically

---

## 🔐 Credentials in Poll SCM

* Jenkins uses **Git credentials**
* These are configured in:

  * Source Code Management (SCM) section
* Poll SCM uses **same credentials** to access repository

🧠 Analogy
🔑 Jenkins uses key to open Git door and check for changes

---

## ✅ Advantages of Poll SCM

✔ Easy to setup
✔ Works even without webhooks
✔ Useful for **legacy systems**
✔ Automatic build trigger

---

## ❌ Disadvantages of Poll SCM

❌ Jenkins checks Git every time → **extra load**
❌ Can create **unnecessary traffic** to Git
❌ Build is **not instant** (depends on polling time)
❌ Not recommended for large projects

---

## 🔄 Poll SCM vs Webhook (Simple)

| Feature           | Poll SCM          | Webhook              |
| ----------------- | ----------------- | -------------------- |
| Who starts action | Jenkins           | Git                  |
| Checking method   | Repeated checking | Instant notification |
| Load              | High              | Low                  |
| Speed             | Delayed           | Fast                 |
| Recommended       | ❌ Not much        | ✅ Yes                |

🧠 Analogy
📬 Checking mailbox → Poll SCM
🔔 Doorbell → Webhook

---

## 🧪 When Do Real Projects Use Poll SCM?

✅ Old systems (no webhook support)
✅ Internal Git servers
✅ Small projects
❌ High-traffic repositories

---

## ✅ One-Line Summary

**Poll SCM means Jenkins checks the Git repository again and again at a fixed time, and if it finds new code, it automatically starts the build — just like checking a mailbox repeatedly.** 📬🔁

---
