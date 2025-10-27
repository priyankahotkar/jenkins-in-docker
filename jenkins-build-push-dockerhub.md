⚙️ 1️⃣ Prerequisites

Ensure your Jenkins container was started with:

-v /var/run/docker.sock:/var/run/docker.sock

This socket mount lets Jenkins talk to the host’s Docker daemon — effectively giving Jenkins “Docker superpowers” from inside a container.

✅ If you followed our earlier installation commands, this is already done.

🧩 2️⃣ Install Docker CLI Inside Jenkins Container

Exec into your Jenkins container:

docker exec -it jenkins bash

Then install Docker CLI:

apt update && apt install -y docker.io

Verify:

docker --version

🔑 3️⃣ Add Docker Hub Credentials in Jenkins

Go to Jenkins Dashboard → Manage Jenkins → Credentials → System → Global credentials

Click “Add Credentials”

Kind: Username and Password

Username: Your Docker Hub username

Password: Your Docker Hub access token or password

ID: e.g., dockerhub

🧱 4️⃣ Create a Jenkins Pipeline (Declarative Example)

Create a new Pipeline Job → use this Jenkinsfile:

pipeline {
agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        IMAGE_NAME = 'yourdockerhubusername/yourimagename'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/yourusername/your-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh "docker push ${IMAGE_NAME}:latest"
                }
            }
        }
    }

}

🧠 5️⃣ Verify It Works

Run the Jenkins pipeline.

Watch logs — you should see:

Docker building image successfully.

Docker login success message.

Docker push uploading layers to Docker Hub.

Go to your Docker Hub → Your repository → You’ll see the pushed image 🎯

🧰 6️⃣ Troubleshooting Common Issues
Issue Cause Fix
docker: command not found Docker CLI not installed inside Jenkins container Install Docker using apt install docker.io
permission denied /var/run/docker.sock Jenkins user not part of docker group Run usermod -aG docker jenkins inside container and restart
unauthorized: authentication required Wrong Docker Hub credentials Re-add credentials with correct username/token
✅ Conclusion

👉 Yes, you can build and push Docker images to Docker Hub using Jenkins even if Jenkins runs inside Docker —
you just need to:

Mount the Docker socket

Install the Docker CLI

Configure credentials properly

After that, Jenkins will behave just like a native host CI/CD system 🚀
