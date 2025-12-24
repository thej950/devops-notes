### **Uploading Docker Images to a Private Nexus Repository** 🚀  

Nexus Repository Manager can be used to **store and manage private Docker images** just like AWS ECR or Docker Hub. Below is a step-by-step guide to **set up Nexus as a private Docker registry** and push Docker images to it.  

---

## **1️⃣ Install & Set Up Nexus Repository Manager**
1. **Download Nexus Repository Manager**  
   - Download from: [https://help.sonatype.com/repomanager3/download](https://help.sonatype.com/repomanager3/download)  
   - Extract and start Nexus:
     ```bash
     ./nexus start
     ```
   - Default URL: `http://localhost:8081`  

2. **Log in to Nexus**  
   - Default **username**: `admin`  
   - Default **password**: Found in `admin.password` file inside the Nexus data directory.  

3. **Create a Private Docker Registry in Nexus**  
   - Go to **Repositories → Create Repository**  
   - Select **Docker (hosted)**  
   - Name: `my-docker-repo`  
   - Port: `5000` (or any available port)  
   - Enable **Allow anonymous docker pull** if needed.  
   - Save and Start the repository.  

---

## **2️⃣ Configure Docker to Use Nexus as a Private Registry**
Since we’re running Nexus on **localhost:5000**, we need to allow insecure registries:  

1. **Modify Docker Daemon Config (`/etc/docker/daemon.json`)**  
   ```json
   {
     "insecure-registries": ["localhost:5000"]
   }
   ```
2. **Restart Docker**  
   ```bash
   systemctl restart docker
   ```

---

## **3️⃣ Log in to Nexus Docker Registry**
Run the following command to **log in**:  

```bash
docker login -u admin -p yourpassword localhost:5000
```

---

## **4️⃣ Build a Docker Image**
Create a **Dockerfile** and build the image:  

```dockerfile
FROM nginx
MAINTAINER YourName
EXPOSE 80
```

```bash
docker build -t mynginx .
```

---

## **5️⃣ Tag the Image for Nexus**
Since we are using **Nexus at localhost:5000**, tag the image as:  

```bash
docker tag mynginx localhost:5000/mynginx
```

---

## **6️⃣ Push the Image to Nexus**
Now, push the image to the private repository:  

```bash
docker push localhost:5000/mynginx
```

✅ **Success!** Your image is now stored in your private Nexus repository. 🎉  

---

## **7️⃣ Pull & Use the Private Image**
To **pull the image** from Nexus on another machine (or after restarting):  

```bash
docker pull localhost:5000/mynginx
```

To **run the container** from the private registry:  

```bash
docker run -d -p 80:80 localhost:5000/mynginx
```

---

## **Final Summary**
| Step | Command |
|------|---------|
| **Start Nexus** | `./nexus start` |
| **Log in to Nexus** | `docker login -u admin -p yourpassword localhost:5000` |
| **Build Image** | `docker build -t mynginx .` |
| **Tag Image** | `docker tag mynginx localhost:5000/mynginx` |
| **Push Image to Nexus** | `docker push localhost:5000/mynginx` |
| **Pull Image from Nexus** | `docker pull localhost:5000/mynginx` |
| **Run Container** | `docker run -d -p 80:80 localhost:5000/mynginx` |

Would you like a guide on **setting up authentication for a more secure Nexus registry**? 🚀