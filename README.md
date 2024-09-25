# Car Rental Website Hosting

## Overview
This project demonstrates the setup of a car rental website hosted on AWS EC2, using Docker and Jenkins for CI/CD. The application is containerized with Docker, and the deployment process is automated through a Jenkins pipeline.

## Prerequisites
- AWS EC2 instance
- Jenkins
- Docker
- GitHub repository
- Docker Hub account

## Steps

### 1. Launch EC2 Instance
Launch an Amazon EC2 instance with the following configurations:
- **AMI:** Amazon Linux 2 Kernel 5.10 AMI 2.0.20240916.0 x86_64 HVM gp2
- **Instance Type:** t3.micro
- **Security Group:** DevOps (Allowing all traffic)
- **Storage:** 1 volume, 8 GiB

### 2. Connect to EC2 Instance
Use SSH to connect to the EC2 instance.
```bash
ssh -i <your-key.pem> ec2-user@<ec2-instance-public-ip>
```

### 3. Install Git
Install Git on the EC2 instance to clone your repository.
```bash
yum install git -y
```

### 4. Install Jenkins
Install Jenkins for continuous integration and automation of the deployment process.
```bash
amazon-linux-extras install java-openjdk11 -y
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
yum install jenkins -y
```
### 5. Install Docker
Install Docker to containerize the web application.
```bash
yum install docker -y
```

### 6. Start Jenkins and Docker
Start Jenkins and Docker services on the EC2 instance.
```bash
systemctl start jenkins
systemctl start docker
```

### 7. Create Dockerfile
Create a Dockerfile for building the Docker image of the web application.
```
FROM httpd
COPY {filedir} /usr/local/apache2/htdocs/
```

### 8. Clone the GitHub Repository
Clone your project repository from GitHub to the EC2 instance.
```
git clone https://github.com/ManikantaBobbili/CarRentalsWebHost.git
```

### 9. Configure Jenkins Pipeline
Set up a Jenkins pipeline to automate the building and deployment of the Docker image.
## Jenkins Pipeline Script:
```
pipeline {
    agent any
    stages {
        stage('Clone Code') {
            steps {
                git 'https://github.com/ManikantaBobbili/CarRentalsWebHost.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t carrental .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry(credentialsId: 'docker_hub_id', url: 'https://index.docker.io/v1/') {
                    sh 'docker tag carrental manikantabobbili/rental:v1'
                    sh 'docker push manikantabobbili/rental:v1'
                }
            }
        }
        stage('Deploy Docker Image') {
            steps {
                sh 'docker run -itd --name rentalv1 -p 9090:80 carrental'
            }
        }
    }
}
```

### Conclusion
By following the steps above, you can successfully host the car rental website on an AWS EC2 instance, using Docker to containerize the application and Jenkins to automate the CI/CD process.

[GitHub Repository](https://github.com/ManikantaBobbili/CarRentalsWebHost)

[Docker Hub Repository](https://hub.docker.com/r/manikantabobbili/rental)
