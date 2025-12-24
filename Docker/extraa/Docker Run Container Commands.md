# 🐳 Docker Run & Container Commands

---

## 🔹 Run Temporary Container (`--rm`)

```bash
docker run --rm -it my-image /bin/bash
```

### Meaning:

* `--rm` → Container is **automatically deleted** when it exits
* `-it` → Interactive terminal
* `/bin/bash` → Open shell inside container

🧠 **Analogy:**
Like a **temporary guest room** → once guest leaves, room is cleaned automatically.

---

## 🔹 Run Container with Environment Variables

```bash
docker run -it --rm -p 5432:5432 \
-e POSTGRES_USER=myuser \
-e POSTGRES_PASSWORD=mypassword \
postgres:9.6.10-alpine
```

### Meaning:

* `-e` / `--env` → Pass environment variables
* Used mainly for **DB credentials, configs**

🧠 **Analogy:**
Giving **username & password** to a new employee on joining day.

---

## 🔹 Run Container in Detached Mode

```bash
docker run -d -P --name my-container my-image
```

### Meaning:

* `-d` → Run in background
* `-P` → Publish all exposed ports automatically

🧠 **Analogy:**
Start a **washing machine** and leave home 😄

---

## 🔹 Run Container with Custom Port

```bash
docker run -p 9090:80 my-image
```

### Meaning:

* `9090` → Host machine port
* `80` → Container port

🧠 **Analogy:**
Door number changed outside, room number same inside.

---

## 🔹 Run Container in a Loop

```bash
docker run --name my-container alpine \
/bin/sh -c "while true; do sleep 2; date; done"
```

### Meaning:

* Prints date **every 2 seconds**
* Runs continuously

🧠 **Analogy:**
A digital clock refreshing time every few seconds.

---

## 🔹 Save Container ID to File

```bash
docker run --cidfile /tmp/cid.txt ubuntu echo "hi!"
```

### Meaning:

* Saves container ID into a file

🧠 **Analogy:**
Writing **ticket number** on a paper for tracking.

---

## 🔹 Auto-Terminating Container

```bash
docker run busybox --name busybox echo "hello world"
```

### Meaning:

* Container runs command
* Finishes work
* Stops automatically

🧠 **Analogy:**
Calculator → press button → shows result → closes.

---

## 🔹 Restart Policies

Used to control **automatic container restarts**

### 1️⃣ no (default)

```bash
docker run --restart=no my-container
```

➡ Never restart

### 2️⃣ always

```bash
docker run --restart=always my-container
```

➡ Always restart

### 3️⃣ on-failure

```bash
docker run --restart=on-failure my-container
```

Limit retries:

```bash
docker run --restart=on-failure:5 my-container
```

### 4️⃣ unless-stopped

```bash
docker run --restart=unless-stopped my-container
```

### Change restart policy

```bash
docker update --restart=unless-stopped my-container
```

### Check restart count

```bash
docker inspect -f "{{ .RestartCount }}" my-container
```

🧠 **Analogy:**
Mobile auto-restarts after crash.

---

## 🔹 Rename Container

```bash
docker container rename old-name new-name
```

---

## 🔹 Push Docker Images to Docker Hub

```bash
docker tag my-image:1.0.1 navathej408/my-image:1.0.1
docker push navathej408/my-image:1.0.1
```

### Steps:

1. Login DockerHub
2. Tag image with username
3. Push image

🧠 **Analogy:**
Label box before sending courier.

---

## 🔹 `docker run -it`

```bash
docker run -it busybox /bin/sh
```

### Meaning:

* `-i` → Interactive
* `-t` → Terminal access

---

## 🔹 Restart Container with Delay

```bash
docker restart -t 15 my-container
```

➡ Waits **15 seconds** before restart

---

## 🔹 Pause & Unpause Container

```bash
docker pause my-container
docker unpause my-container
```

🧠 **Analogy:**
Pause & resume a YouTube video.

---

## 🔹 Execute Command in Running Container

```bash
docker exec -it my-container bash
docker exec -d my-container touch /path/file
```

---

## 🔹 Dangling Images

### What are they?

* Untagged images (`<none>:<none>`)
* Not used by any container

### Commands:

```bash
docker images -f dangling=true
docker image prune
docker system prune
```

🧠 **Analogy:**
Unused files filling disk space.

---

## 🔹 Copy Files

```bash
docker cp my-container:/folder/report.xml .
docker cp init.conf my-container:/conf/env/init.conf
```

---

## 🔹 Container Exit Codes

```bash
echo $?
```

| Code | Meaning           |
| ---- | ----------------- |
| 125  | docker run failed |
| 127  | command not found |
| 126  | cannot execute    |
| 130  | Ctrl + C          |
| 137  | SIGKILL           |
| 143  | SIGTERM           |

Check signals:

```bash
kill -L
```

---

## 🔹 Container Basics

* Container = **running image**
* Changes inside container are **temporary**
* Data is lost when container stops

Create container:

```bash
docker create my-image
docker create --name my-container my-image
```

List containers:

```bash
docker container ls
docker container ls -a
```

---

## 🔹 Docker Cache

* Docker builds images **layer by layer**
* Layers are **immutable**
* Cache makes builds faster
* `FROM` instruction **does not use cache**

Check layers:

```bash
docker history my-image --no-trunc
docker inspect -f '{{json.RootFS.Layers}}' my-image | jq .
```

---

## 🔹 Build Docker Images

```bash
docker build .
docker build -t my-image:1.0.1 .
docker build -f build/Dockerfile -t my-image:tag .
docker build https://github.com/thej950/project-1.git#main -f helloworld/Dockerfile
```

🧠 **Analogy:**
Dockerfile = recipe
Docker image = cooked food 🍲

---
