# 📌 What is Master–Slave in Jenkins?

**Master–Slave is an architecture** where:

* **Master (Controller)** manages Jenkins
* **Slave (Agent)** does the actual job execution

👉 Instead of running **all jobs on one Jenkins server**, we **share the work** with other machines.

---

## 🧠 Real-Life Analogy (Office)

🏢 **Office Manager (Master)**

* Assigns work
* Tracks progress
* Does NOT do heavy work

👷 **Employees (Slaves)**

* Do actual work
* Build, test, deploy

➡️ Manager distributes tasks to employees to avoid overload.

---

## ❓ Why do we need Slave machines?

Without slaves:

* Master becomes **slow**
* CPU & RAM overload
* Jobs wait in queue

With slaves:

* Jobs run **in parallel**
* Faster builds
* Better performance
* Scalable CI/CD

---

## 🏗️ Architecture Flow (Simple)

```
Developer → Git Push
        ↓
     Jenkins Master
        ↓
   Assign Job to Slave
        ↓
   Slave runs build/test/deploy
```

---

## 🔹 Key Components Explained

### 🟢 Master (Controller)

* Jenkins UI
* Job configuration
* User management
* Scheduling jobs
* Delegates work

### 🟢 Slave (Agent)

* Executes jobs
* Needs Java
* Connects to master
* Has its own workspace

---

## 🔹 What is `slave.jar`?

* Small Java file provided by Jenkins
* Used to **connect slave to master**
* Contains agent communication setup

👉 Like an **ID card** for employee to enter office.

---

## 🔧 High-Level Setup Steps (Concept Only)

### 1️⃣ Master Machine

* Install Java, Git, Maven, Jenkins
* Open Jenkins UI
* Create jobs
* Add slave nodes

### 2️⃣ Slave Machine

* Install Java
* Enable SSH access
* Create workspace directory
* Connect to master

---

## 🔐 Why SSH / Passwordless Login?

* Jenkins master must **log in to slave automatically**
* No manual password typing
* Secure & automated

👉 Like giving **office access card** instead of asking password every time.

---

## 🔹 Executors (Very Important)

**Executors = how many jobs a slave can run at the same time**

| Executors | Meaning            |
| --------- | ------------------ |
| 1         | One job at a time  |
| 2         | Two jobs parallel  |
| 4         | Four jobs parallel |

---

## 🔹 Labels (Very Simple)

**Label = nickname for a slave**

Example:

* Slave name: `slave1`
* Label: `myslave`

👉 Jobs can say:
**“I want to run only on `myslave`”**

---

## 🧪 Real Project Use Case

| Job               | Runs On         |
| ----------------- | --------------- |
| Development build | Master          |
| Testing job       | Slave           |
| Heavy load        | Slave           |
| Production deploy | Dedicated Slave |

---

# Practical Example 

## 🔹 Step 0: Launch Servers

* Launch **2 Ubuntu EC2 instances**

  * **jenkins-master**
  * **jenkins-slave**
* Allow ports **22 (SSH)** and **8080 (Jenkins)** in Security Group.

---

## 🔹 Step 1: Jenkins Master Setup

1. SSH into **jenkins-master**
2. Update system

   ```bash
   sudo apt update -y
   ```
3. Install Java, Git, Maven

   ```bash
   sudo apt install openjdk-11-jdk git maven -y
   ```
4. Install Jenkins

   ```bash
   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
   /usr/share/keyrings/jenkins-keyring.asc > /dev/null

   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
   https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
   /etc/apt/sources.list.d/jenkins.list > /dev/null

   sudo apt update
   sudo apt install jenkins -y
   ```
5. Start Jenkins

   ```bash
   sudo systemctl start jenkins
   ```
6. Open browser → `http://<master_public_ip>:8080`
7. Complete Jenkins UI setup (admin password, plugins).

---

## 🔹 Step 2: Jenkins Slave Setup

1. SSH into **jenkins-slave**
2. Install Java

   ```bash
   sudo apt update && sudo apt install openjdk-11-jdk -y
   ```
3. Edit SSH config

   ```bash
   sudo vi /etc/ssh/sshd_config
   ```

   Change:

   ```
   PasswordAuthentication yes
   ```
4. Restart SSH

   ```bash
   sudo systemctl restart ssh
   ```
5. Set password for ubuntu user

   ```bash
   sudo passwd ubuntu
   ```
6. Exit slave.

---

## 🔹 Step 3: Passwordless SSH (Master → Slave)

1. SSH into **master**
2. Switch to Jenkins user

   ```bash
   sudo su - jenkins
   ```
3. Generate SSH key

   ```bash
   ssh-keygen
   ```
4. Copy key to slave

   ```bash
   ssh-copy-id ubuntu@<slave_private_ip>
   ```
5. Test login

   ```bash
   ssh ubuntu@<slave_private_ip>
   ```

   ✅ Login should work **without password**

---

## 🔹 Step 4: Prepare Slave Agent

1. On **slave machine**
2. Download agent jar

   ```bash
   wget http://<master_public_ip>:8080/jnlpJars/slave.jar
   chmod 777 slave.jar
   ```
3. Create workspace directory

   ```bash
   mkdir /home/ubuntu/dir1
   chmod 777 /home/ubuntu/dir1
   ```
4. Exit slave.

---

## 🔹 Step 5: Configure Slave in Jenkins UI

1. Jenkins Dashboard → **Manage Jenkins**
2. **Manage Nodes and Clouds**
3. **New Node**

   * Name: `slave1`
   * Type: **Permanent Agent**
4. Configure:

   * **Executors**: `2`
   * **Remote root dir**: `/home/ubuntu/dir1`
   * **Labels**: `myslave`
   * **Usage**: Only build jobs with label expressions
   * **Launch method**: *Launch agent via execution of command*
5. Launch Command:

   ```bash
   ssh ubuntu@<slave_private_ip> java -jar /home/ubuntu/slave.jar
   ```
6. Save
   ✅ Master–Slave setup completed

---

## 🔹 Step 6: Job Execution (Real-Time Flow)

1. Create **Development Job**

   * Runs on **master**
2. Create **Testing Job**
3. Configure Testing Job:

   * **Restrict where this project can run**
   * Label: `myslave`
4. Trigger Development job
5. After success → Testing job runs automatically on **slave**

---


# Example-2 practicals 

## 🔹 Jenkins Web UI – Add Slave (Agent)

### Step 1: Open Jenkins

* Open Jenkins Web UI
* Go to **Manage Jenkins → Nodes → New Node**

---

### Step 2: Add Node Configuration

* **Node name**: `slave2`
* **Type**: Permanent Agent
* Click **Create**

---

### Step 3: Configure Slave Details

* **Number of executors**: `2`
  → Means 2 jobs can run in parallel
* **Remote root directory**: `/home/jenkins`
  → Workspace path on slave
* **Labels**: `jenkins-agent01`
  → Used to target this slave
* **Usage**: Use this node as much as possible

---

### Step 4: Launch Method (SSH)

* **Launch method**: Launch agent via SSH
* **Host**: `172.31.27.204` (slave private IP)
* **Credentials**:

  * Username: `jenkins`
  * Password: `*****`
  * ID: `jenkins-key`
  * Description: `jenkins-key`
* **Host Key Verification**: Known hosts file verification strategy
* **Availability**: Keep this agent online as much as possible
* Click **Save**

✅ Slave node will automatically come **Online**

---

## 🔹 Run Builds on Slave Machine

### Jenkins Pipeline Example

```groovy
pipeline {
    agent {
        label 'jenkins-agent01'
    }
    stages {
        stage('Download Code') {
            steps {
                git 'https://github.com/thej950/maven.git'
            }
        }
    }
}
```

---

## 🔹 What Happens Internally

* Jenkins checks label **jenkins-agent01**
* Job is assigned to **slave machine**
* Git code is downloaded into
  `/home/jenkins/workspace/<job-name>` on **slave**
* Jenkins master only controls orchestration

---

## 🟢 Master–Slave Benefits (Interview Points)

* Better performance
* Parallel job execution
* Scalable architecture
* Reduced master load
* Supports large CI/CD pipelines

---

## 🎯 Interview-Ready Answer (Simple)

**Jenkins master–slave architecture helps distribute workload. The master manages jobs and users, while slave machines execute builds. This improves performance, allows parallel execution, and avoids overloading the master.**

---
