# 🚀 Uploading Docker Images to a Private Nexus Repository

*(Beginner-Friendly & Verified Guide)*

Nexus Repository Manager can be used as a **private Docker registry**, similar to **Docker Hub** or **AWS ECR**.

👉 This means:

* You can **store your own Docker images**
* Images stay **inside your company / local network**
* No public access unless you allow it

---

## 🧩 What We Are Doing (Simple Words)

1. Install Nexus
2. Create a **Docker hosted repository**
3. Tell Docker to trust Nexus
4. Build a Docker image
5. Push image to Nexus
6. Pull & run image from Nexus

---

## 1️⃣ Install & Set Up Nexus Repository Manager

### 🔹 Download Nexus

Download from:
👉 [https://help.sonatype.com/repomanager3/download](https://help.sonatype.com/repomanager3/download)

Extract and start Nexus:

```bash
./nexus start
```

Default Nexus URL:

```
http://localhost:8081
```

🧠 **Analogy:**
Nexus is like a **private warehouse** where Docker images are stored.

---

### 🔹 Login to Nexus

* Username: `admin`
* Password: Found inside:

```
<nexus-data>/admin.password
```

👉 After first login, Nexus will ask you to **change the password** (recommended).

---

### 🔹 Create a Private Docker Repository

1. Go to **Repositories → Create Repository**
2. Select **Docker (hosted)**
3. Repository Name:

   ```
   my-docker-repo
   ```
4. HTTP Port:

   ```
   5000
   ```
5. (Optional) Enable:

   * ✅ Allow anonymous docker pull (only if needed)
6. Click **Create Repository**

🧠 **Meaning:**
Now Nexus is ready to act as a **Docker registry on port 5000**.

---

## 2️⃣ Configure Docker to Use Nexus Registry

Since Nexus is running on **HTTP (not HTTPS)**, Docker must trust it.

### 🔹 Edit Docker Daemon Config

Open file:

```bash
sudo vim /etc/docker/daemon.json
```

Add:

```json
{
  "insecure-registries": ["localhost:5000"]
}
```

### 🔹 Restart Docker

```bash
sudo systemctl restart docker
```

🧠 **Why this is needed?**
Docker blocks insecure registries by default.
We are telling Docker: *“It’s okay to trust this Nexus server.”*

---

## 3️⃣ Login to Nexus Docker Registry

Login using Docker CLI:

```bash
docker login localhost:5000
```

Enter:

* Username: `admin`
* Password: `<your-nexus-password>`

✅ If successful, Docker can now push images to Nexus.

---

## 4️⃣ Build a Docker Image

### 🔹 Create Dockerfile

```dockerfile
FROM nginx
MAINTAINER YourName
EXPOSE 80
```

⚠️ **Note (Best Practice):**
`MAINTAINER` is deprecated, but still works.
Modern way:

```dockerfile
LABEL maintainer="YourName"
```

### 🔹 Build Image

```bash
docker build -t mynginx .
```

🧠 **Meaning:**
You created a Docker image named `mynginx` locally.

---

## 5️⃣ Tag the Image for Nexus

Docker needs to know **where to push the image**.

### 🔹 Tag Image

```bash
docker tag mynginx localhost:5000/mynginx
```

🧠 **Analogy:**
Tagging is like writing the **destination address** on a parcel.

---

## 6️⃣ Push Image to Nexus

Push the image:

```bash
docker push localhost:5000/mynginx
```

✅ **Success!**
Your Docker image is now stored inside Nexus 🎉

You can verify it:

* Nexus UI → Repositories → `my-docker-repo`
* You will see `mynginx`

---

## 7️⃣ Pull & Run Image from Nexus

### 🔹 Pull Image

```bash
docker pull localhost:5000/mynginx
```

### 🔹 Run Container

```bash
docker run -d -p 80:80 localhost:5000/mynginx
```

👉 Access in browser:

```
http://localhost
```

You should see **Nginx default page**.

---

## 📌 Final Summary (Quick Revision)

| Step           | Command                                         |
| -------------- | ----------------------------------------------- |
| Start Nexus    | `./nexus start`                                 |
| Login to Nexus | `docker login localhost:5000`                   |
| Build Image    | `docker build -t mynginx .`                     |
| Tag Image      | `docker tag mynginx localhost:5000/mynginx`     |
| Push Image     | `docker push localhost:5000/mynginx`            |
| Pull Image     | `docker pull localhost:5000/mynginx`            |
| Run Container  | `docker run -d -p 80:80 localhost:5000/mynginx` |

---

## ✅ Is Your Content Correct?

✔ Yes, **overall steps are correct**
✔ Nexus Docker hosted repo usage is correct
✔ Docker tagging & push flow is correct
✔ Insecure registry config is correct

🔧 Minor improvements I applied:

* Simplified login command
* Explained *why* insecure registry is needed
* Mentioned MAINTAINER best practice
* Clear beginner explanations

---

