# 🚀 Jenkins Installation in Docker (Windows)

This guide provides **step-by-step commands** to install and run **Jenkins inside Docker** on **Windows** using **Docker Desktop**.

---

## 🧩 1️⃣ Prerequisites

Ensure you have:
- **Windows 10/11** (with WSL2 preferred)
- **Docker Desktop** installed and running
- **PowerShell** or **Command Prompt** (run as Administrator)

---

## ⚙️ 2️⃣ Pull Jenkins LTS Image

```powershell
docker pull jenkins/jenkins:lts-jdk17
```

---

## 🧰 3️⃣ Create and Run Jenkins Container

```powershell
docker run -d `
  --name jenkins `
  --restart=on-failure `
  -p 8080:8080 -p 50000:50000 `
  -v "C:/Users/PRIYANKA/jenkins_home:/var/jenkins_home" `
  -v "//var/run/docker.sock:/var/run/docker.sock" `
  -v "C:/Program Files/Docker/Docker/resources/bin/docker.exe:/usr/bin/docker" `
  jenkins/jenkins:lts-jdk17
```

> **Notes:**
> - The `^` symbol is PowerShell’s line continuation character.  
> - Paste the entire command on one line if using CMD.  
> - Docker will automatically create the volume `jenkins_home` if it doesn't exist.

---

## 🧾 4️⃣ Check Jenkins Container Status

```powershell
docker ps
```

You should see a running container named **jenkins**.

---

## 🔑 5️⃣ Retrieve Jenkins Admin Password

Try the following command:

```powershell
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

If that fails (depending on your Docker Desktop setup), use:

```powershell
docker exec -it jenkins type C:\ProgramData\Docker\volumes\jenkins_home\_data\secrets\initialAdminPassword
```

Copy the password shown in the terminal.

---

## 🌐 6️⃣ Access Jenkins Dashboard

Open your browser and navigate to:

```
http://localhost:8080
```

Paste the **initial admin password** when prompted.

---

## ⚙️ 7️⃣ Install Plugins & Create Admin User

1. Click **“Install suggested plugins”**  
2. Wait for installation to complete  
3. Create your **admin username and password**

---

## 🔄 8️⃣ Manage Jenkins Container

```powershell
# Restart Jenkins
docker restart jenkins

# Stop Jenkins
docker stop jenkins

# Start Jenkins again
docker start jenkins

# Remove Jenkins container
docker rm -f jenkins

# Remove Jenkins data volume (optional)
docker volume rm jenkins_home
```

---

## ✅ 9️⃣ Verification

- Visit Jenkins at: [http://localhost:8080](http://localhost:8080)  
- Log in with your created credentials  
- Jenkins is now ready to use 🚀  

---

## 🧭 Directory & Volume Summary

- Jenkins data is stored in the Docker-managed volume:
  ```
  jenkins_home:/var/jenkins_home
  ```
- It persists even if the container stops or restarts.

---

## 🧠 Optional — Docker Compose Setup

If you prefer managing Jenkins with Docker Compose:

Create a file named `docker-compose.yml`:
```yaml
version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins
    restart: unless-stopped
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock

volumes:
  jenkins_home:
```

Run Jenkins with one command:
```powershell
docker-compose up -d
```

Stop it with:
```powershell
docker-compose down
```

---

## 💼 Jenkins is Now Ready

Access at: **[http://localhost:8080](http://localhost:8080)**  
Enjoy your fully containerized Jenkins CI/CD environment. 🎯
