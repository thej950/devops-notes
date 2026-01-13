# How Blue Green Process works  
Project Link: [Click Here](https://github.com/thej950/GitHub-Actions-project.git)
---

## 🔍 1️⃣ Check which deployments exist

```bash
kubectl get deployments -n webapp
```

You will see:

```
node-web-blue
node-web-green
```

Both environments are running.

---

## 🔁 2️⃣ Rollback (Instant)

If **Green has problem**, send users back to **Blue**:

```bash
kubectl patch svc node-web-svc -n webapp -p '{"spec":{"selector":{"version":"blue"}}}'
```

This only changes traffic — **no pod restart**.

---

## 🔍 3️⃣ Verify

```bash
kubectl get svc node-web-svc -n webapp -o yaml
```

You should see:

```
selector:
  version: blue
```

Now users are using **Blue version**.

---

## ⏳ 4️⃣ Grace period

After switching to Green in deployment:

* Wait **15–30 minutes**
* Monitor logs, errors, alerts

Do NOT touch Blue yet.

---

## 🧹 5️⃣ After Green is stable

Now remove Blue pods safely:

```bash
kubectl scale deployment node-web-blue --replicas=0 -n webapp
```

Now:

* Blue pods → stopped
* Green pods → serving users

---

## ❗ Important rule

❌ Do NOT delete Blue deployment
Only scale it to 0

So rollback is always possible.

---

## 🧠 One interview line

**In Blue-Green deployment, we keep the old version running during a grace period, so rollback is just switching the Service selector — no redeploy, no downtime.** 🚀


Good question – this is **exactly how real Blue-Green works in production** 👇

Assume:

* **Green is live**
* **Blue is old (scaled to 0)**
  Now a **new version** comes.

---

# After Some days if new verion image come with blue then same process green is old image blue is new image   

## 🔁 Step-1: Deploy new version to Blue

Because **Green is serving users**, Blue is free.

```bash
kubectl set image deployment/node-web-blue \
node-web=acrserver/nodeapp:v3 \
-n webapp
```

Then bring Blue back:

```bash
kubectl scale deployment node-web-blue --replicas=2 -n webapp
```

Now:

* Green → old version (live)
* Blue → new version (testing)

---

## 🔍 Step-2: Test Blue

Test using:

* Pod IP
* Internal service
* Curl
* Monitoring

Make sure:

* App is working
* No crashes

---

## 🔄 Step-3: Switch traffic to Blue

```bash
kubectl patch svc node-web-svc -n webapp -p '{"spec":{"selector":{"version":"blue"}}}'
```

Now:

* Users → Blue (new version)

---

## ⏳ Step-4: Grace period

Wait 15–30 minutes.

If error → rollback:

```bash
kubectl patch svc node-web-svc -n webapp -p '{"spec":{"selector":{"version":"green"}}}'
```

---

## 🧹 Step-5: Scale down Green

After stable:

```bash
kubectl scale deployment node-web-green --replicas=0 -n webapp
```

---

## 🧠 One interview line

**In Blue-Green, every new release is deployed to the inactive color, tested, then traffic is switched, giving instant rollback and zero downtime.** 🚀
