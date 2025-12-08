# ✅ **K8s (EKS) → Store MySQL Data on EFS → Regular Backups → S3**

This is the industry-standard architecture:

```
MySQL Pod (StatefulSet)
        ↓
      EFS (shared, durable storage)
        ↓
 Backup Script (cron job / sidecar)
        ↓
        S3 (long-term storage)
```

Let me explain everything clearly + give full working YAML + scripts.

---

# ✅ **1. Why EFS for Kubernetes MySQL?**

EFS advantages:

✔ Survives Node failures
✔ Accessible from ANY EKS node
✔ Auto-scales storage
✔ Works for multi-AZ
✔ Perfect for storing MySQL data directory `/var/lib/mysql`

Better than hostPath (data lost)
Better than EBS (single-AZ only + stuck to node)

---

# ✅ **2. How to Setup MySQL Using EFS in Kubernetes**

## **Step 1 — Install EFS CSI Driver**

```bash
kubectl apply -k "github.com/kubernetes-sigs/aws-efs-csi-driver/deploy/kustomization/"
```

---

## **Step 2 — Create EFS StorageClass**

Replace `fs-1234567890abcdef` with your EFS ID.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: efs-sc
provisioner: efs.csi.aws.com
parameters:
  provisioningMode: efs-ap
  fileSystemId: fs-1234567890abcdef
  directoryPerms: "700"
```

Apply:

```
kubectl apply -f efs-storageclass.yaml
```

---

## **Step 3 — Use PVC with `efs-sc` for MySQL**

```yaml
volumeClaimTemplates:
  - metadata:
      name: mysql-storage
    spec:
      accessModes: ["ReadWriteMany"]
      storageClassName: efs-sc
      resources:
        requests:
          storage: 5Gi
```

Now every MySQL pod uses EFS.

---

# ✅ **3. How to Backup MySQL from EFS → S3**

You have 3 good options:

---

# ⭐ **Option A — CronJob inside Kubernetes (Best)**

Runs daily inside cluster → exports MySQL dump → uploads to S3.

### CronJob Example:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: mysql-backup
  namespace: webapp
spec:
  schedule: "0 1 * * *"     # daily at 1 AM
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: backup
              image: amazonlinux:2
              command:
                - /bin/bash
                - -c
                - |
                  yum install -y mysql aws-cli
                  mysqldump -h mysql -u root -pwhizlabs company > /backup/company.sql
                  aws s3 cp /backup/company.sql s3://mybucket/mysql-backups/company-$(date +%F).sql
              volumeMounts:
                - name: backup-folder
                  mountPath: /backup
          volumes:
            - name: backup-folder
              emptyDir: {}
          restartPolicy: OnFailure
```

Replace:

* `mysql` → DB service name
* `company` → DB name
* `mybucket` → Your S3 bucket name

---

# ⭐ **Option B — Sidecar Container (Backup container inside MySQL pod)**

The sidecar has:

* Shared volume (`efs`)
* Script that runs hourly/daily
* Uploads backup file to S3

---

# ⭐ **Option C — Run backup script from EC2 or Jenkins**

You mount EFS inside an EC2 instance:

```
sudo mount -t efs fs-xxxx:/ /mnt/efs
```

Then run:

```
aws s3 sync /mnt/efs/mysql-backups s3://yourbucket/backups
```

---

# ✅ **4. Example Backup Script (bash)**

Save as `/usr/local/bin/backup.sh`:

```bash
#!/bin/bash
DATE=$(date +%F-%H-%M)
mysqldump -h mysql -u root -pwhizlabs company > /backup/company-$DATE.sql
aws s3 cp /backup/company-$DATE.sql s3://mybucket/mysql-backups/
```

Make executable:

```
chmod +x backup.sh
```

Run manually:

```
./backup.sh
```

---

# 🔥 **Best Architecture Recommendation**

✔ MySQL database pod in Kubernetes
✔ Persistent volume: **EFS**
✔ Daily scheduled backups → **S3**
✔ Optionally enable lifecycle policy in S3 (archive to glacier)

This is fully production ready.

---
