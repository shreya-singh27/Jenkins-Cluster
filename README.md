# Jenkins Cluster Setup using Docker & Kubernetes (k3s)

## Project Purpose

Create a Jenkins Cluster in Docker Container & Kubernetes Pod
**Note:** Do **not** use pre-created images.
  Manually install Jenkins in Docker.
  Deploy a multi-agent Jenkins setup using Kubernetes (k3s).

---

## Project Folder Structure

```
Jenkins-Cluster/
├── jenkins-master/
│   └── Dockerfile
├── jenkins-agent/
│   ├── Dockerfile
│   └── jenkins-agent.yaml
├── screenshots/
│   └── (all screenshots)
└── README.md
```

---

## EC2 Setup

We used two **Ubuntu EC2** instances:

1. Jenkins Master (Docker-based)
2. Jenkins Agent (Kubernetes-based)

---

## Step-by-Step Execution

### Step 1: Launch EC2 Instance for Jenkins & k3s

Launched two **Ubuntu EC2 instances** under AWS Free Tier.

* One for Jenkins Master (Docker-based)
* One for Jenkins Agent (k3s-based)

### Step 2: Setup Security Groups

Configured security group to allow:

* SSH (22)
* HTTP (80)
* HTTPS (443)
* Custom TCP (8080) — for Jenkins

### Step 3: Install Docker on Jenkins Master EC2

```
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

### Step 4: Build Jenkins Master Image

#### `jenkins-master/Dockerfile`

```Dockerfile
FROM ubuntu:20.04

USER root

RUN apt-get update && \
    apt-get install -y openjdk-11-jdk wget gnupg curl && \
    wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | apt-key add - && \
    sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list' && \
    apt-get update && \
    apt-get install -y jenkins && \
    apt-get clean;

EXPOSE 8080

CMD ["java", "-jar", "/usr/share/jenkins/jenkins.war"]
```

### Step 5: Run Jenkins Master Container

```
cd jenkins-master
sudo docker build -t custom-jenkins .
sudo docker run -d -p 8080:8080 --name jenkins-master custom-jenkins
```

### Step 6: Access Jenkins Web Interface

Access it from browser:

```
http://<EC2-PUBLIC-IP>:8080
```

### Step 7: Create Jenkins Agent Docker Image

#### `jenkins-agent/Dockerfile`

```Dockerfile
FROM ubuntu:20.04

RUN apt-get update && \
    apt-get install -y openjdk-11-jdk git curl wget unzip

RUN useradd -m -d /home/jenkins jenkins
USER jenkins
WORKDIR /home/jenkins
```

### Step 8: Push Jenkins Agent Image to DockerHub

```
sudo docker build -t jenkins-agent .
sudo docker tag jenkins-agent:latest shreyasing27/jenkins-agent:latest
sudo docker push shreyasing27/jenkins-agent:latest
```

### Step 9: Install Kubernetes (k3s) on Agent EC2

```
curl -sfL https://get.k3s.io | sh -
kubectl get nodes
```

### Step 10: Create Agent Pod YAML & Deploy

#### `jenkins-agent.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: jenkins-agent
spec:
  containers:
  - name: jenkins-agent
    image: shreyasing27/jenkins-agent:latest
    imagePullPolicy: Always
    command: ["sleep", "3600"]
```

```kubectl apply -f jenkins-agent.yaml
kubectl get pods
```

---


##  M-8 Screenshots

All screenshots are stored in `screenshots/` folder:

| Step                   | Screenshot                                                            |
| ---------------------- | --------------------------------------------------------------------- |
| EC2 Instance Setup     | ![](screenshots/EC2%20instance%20setup%20for%20Jenkins%20&%20k3s.png) |
| Security Groups        | ![](screenshots/Setting%20up%20security%20groups%20for%20access.png)  |
| Docker Installation    | ![](screenshots/Installing%20Docker%20on%20EC2.png)                   |
| Jenkins Master Running | ![](screenshots/Jenkins%20Master%20container%20running.png)           |
| Jenkins Web UI         | ![](screenshots/Jenkins%20Web%20UI%20access.png)                      |
| Pulling Agent Image    | ![](screenshots/Pulling%20Jenkins%20Agent%20image.png)                |
| DockerHub Push         | ![](screenshots/Jenkins%20image%20on%20DockerHub.png)                 |

---

## What We Did

* Manually installed Jenkins on Docker
* Created Jenkins Master Docker image
* Created Jenkins Agent Docker image
* Pushed Agent image to DockerHub
* Deployed Agent as Kubernetes Pod via k3s

---

## Author

**Shreya Singh**
GitHub: [@shreya-singh27](https://github.com/shreya-singh27)

---

## Final Notes

This project was executed step-by-step with a focus on learning core concepts manually rather than using shortcuts or automation tools. Every screenshot reflects the real-time effort put into building the Jenkins cluster from scratch.
