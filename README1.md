# 🚀 Chattingo - Mini Hackathon Challenge

A full-stack real-time chat application built with React, Spring Boot, and WebSocket technology. **Your mission**: Containerize this application using Docker and deploy it to Hostinger VPS using Jenkins CI/CD pipeline.

## 🎯 **Hackathon Challenge**
Transform this vanilla application into a production-ready, containerized system with automated deployment!

## 📋 Table of Contents

- [Hackathon Overview](#-hackathon-overview)
- [Architecture Overview](#️-architecture-overview)
- [Technology Stack](#️-technology-stack)
- [Quick Start](#-quick-start)
- [Your Tasks](#-your-tasks)
- [Application Features](#-application-features)
- [Project Structure](#-project-structure)
- [Submission Requirements](#-submission-requirements)
- [Support & Resources](#-support--resources)
- [Judging Criteria](#-judging-criteria)

## 🏆 Hackathon Overview

### 🎯 Project Goals
- **Build & Deploy**: Create Dockerfiles and containerize the application
- **CI/CD Pipeline**: Implement Jenkins automated deployment
- **VPS Deployment**: Deploy on Hostinger VPS using modern DevOps practices

## 🏗️ Architecture Overview

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │   Backend       │    │   Database      │
│   (React)       │◄──►│   (Spring Boot) │◄──►│   (MySQL)       │
│   Port: 80      │    │   Port: 8080    │    │   Port: 3306    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │
         └────── WebSocket ──────┘
```
## 🏗️ DevOPs worklow
![alt text](https://github.com/VirajDalave/chattingo/blob/main/DevSecOps%20workflow.png)

## 🛠️ Technology Stack

### Frontend
- **React 18** - Modern UI framework
- **Redux Toolkit** - State management
- **Material-UI** - Component library
- **Tailwind CSS** - Utility-first CSS
- **WebSocket (SockJS + STOMP)** - Real-time messaging
- **React Router** - Client-side routing

### Backend
- **Spring Boot 3.3.1** - Java framework
- **Spring Security** - Authentication & authorization
- **Spring Data JPA** - Database operations
- **Spring WebSocket** - Real-time communication
- **JWT** - Token-based authentication
- **MySQL** - Database

### DevOps 
- **Docker** - Containerization of application
- **Docker Compose** - Multi-container orchestration (frontend, backend and database)
- **Jenkins** - Automated CICD on GitHub pushes
- **Nginx** - Web server & reverse proxy

## 🚀 Quick Start

#### **Step 1: Fork & Clone**
```bash
# Fork this repository on GitHub: https://github.com/VirajDalave/chattingo
# Then clone your fork
git clone https://github.com/YOUR_USERNAME/chattingo.git
cd chattingo
```

#### **Step 2: Local Development Setup**
Follow **[CONTRIBUTING.md](CONTRIBUTING.md)** for detailed setup instructions.


### **Reference Guides**
- **Detailed Setup & Deployment**: **[CONTRIBUTING.md](CONTRIBUTING.md)**

## 🔧 **MY TASKS**

### **Task 1: Docker Implementation (5 Marks)**

Created the following multi-stage Dockerfiles:

#### **Frontend Dockerfile** (3-stage build)
- Stage 1: Node.js build environment
- Stage 2: Build React application  
- Stage 3: Nginx runtime server

#### **Backend Dockerfile** (3-stage build)
- Stage 1: Maven build environment
- Stage 2: Build Spring Boot application
- Stage 3: JRE runtime

#### **Docker Compose** (Root level)
Create `docker-compose.yml` to orchestrate all services.

**Scoring**: Single Stage (2), Two Stage (4), Multi Stage (5)

### **Task 2: Jenkins CI/CD Pipeline (17 Marks)**

Created a `Jenkinsfile` with these stages:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Git Clone') { 
            // Clone repository from GitHub (2 Marks)
        }
        stage('Image Build') { 
            // Build Docker images for frontend & backend (2 Marks)
        }
        stage('Filesystem Scan') { 
            // Security scan of source code (2 Marks)
        }
        stage('Image Scan') { 
            // Vulnerability scan of Docker images (2 Marks)
        }
        stage('Push to Registry') { 
            // Push images to Docker Hub/Registry (2 Marks)
        }
        stage('Update Compose') { 
            // Update docker-compose with new image tags (2 Marks)
        }
        stage('Deploy') { 
            // Deploy to Hostinger VPS (5 Marks)
        }
         stage('Docker cleanup') { 
            // Clean previous docker images
        }
    }
}
```

### **Task 3: VPS Deployment**
- **Hostinger VPS Setup**: Ubuntu 22.04 LTS, 2GB RAM
- **Domain Configuration**: Setup your domain with DNS
- **Production Deployment**: Automated deployment via Jenkins

**Detailed Instructions**: See **[CONTRIBUTING.md](CONTRIBUTING.md)**

## Installation Guide

#### **Step 1: Clone the repository**
```bash
# Clone the repository
git clone https://github.com/VirajDalave/chattingo.git
cd chattingo
```
#### **Step 2: Install docker and docker-compose**
```bash
sudo apt-get update
sudo apt-get install curl gnupg ca-certificates lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
sudo usermod -aG docker $USER && newgrp docker
```

## Deployment Guide

### Deploy using Docker

#### **Step 1: Build the images**
```bash
docker build -t chattingo-app ./backend
#Edit the frontend Dockerfile REACT_APP_API_URL and REACT_APP_WS_URL with your ip address
sudo vim frontend/Dockerfile
docker build -t chattingo-web ./frontend
```
#### **Step 2: Start the containers**
- create network
  ```bash
  docker network create chattingo  
  ```
  
- database container
  ```bash
  docker run -d -p 3306:3306 --network chattingo -e MYSQL_ROOT_PASSWORD=<YOUR DB PASSWORD> -e MYSQL_DATABASE=chattingo_db --name db mysql:lts
  ```
  
- backend container
  ```bash
  # Generate secure JWT secret (32 bytes = 256 bits)
  openssl rand -base64 32
  #copy this JWT secret
  docker run -d -p 8080:8080 -e JWT_SECRET=<YOUR JWT SECRET> -e SPRING_DATASOURCE_URL=jdbc:mysql://db:3306/chattingo_db?createDatabaseIfNotExist=true -e SPRING_DATASOURCE_USERNAME=root -e SPRING_DATASOURCE_PASSWORD=<YOUR DB PASS> -e CORS_ALLOWED_ORIGINS=http://<your-ip-address> -e CORS_ALLOWED_METHODS= --name app chattingo-app
  
  ```
- frontend container
  ```bash
  docker run -d -p 80:80 --network chattingo --name web chattingo-web
  ```
- Access the application
  Access at localhost:80

### Deploy using docker-compose (recommended)
#### **Step 1: Edit the Dockerfile**
```bash
#Edit the frontend Dockerfile REACT_APP_API_URL and REACT_APP_WS_URL with your ip address
sudo vim frontend/Dockerfile
```
#### **Step 2: Edit the docker compose file**
```bash
#Edit the environment variable values in docker-compose file from the 'Deploy using Docker' section
sudo vim docker-compose.yml
```

#### **Step 3: Deploy the application**
```bash
docker compose up -d
```
Access at localhost:80

### **For troubleshooting**: See **[CONTRIBUTING.md](CONTRIBUTING.md)**

## 📱 Application Features

### Core Functionality
- ✅ User authentication (JWT)
- ✅ Real-time messaging (WebSocket)
- ✅ Group chat creation
- ✅ User profile management
- ✅ Message timestamps
- ✅ Responsive design

### API Endpoints
```
POST   /api/auth/register    - User registration
POST   /api/auth/login       - User login
GET    /api/users            - Get users
POST   /api/chats/create     - Create chat
GET    /api/chats            - Get user chats
POST   /api/messages/create  - Send message
GET    /api/messages/{chatId} - Get chat messages
WS     /ws                   - WebSocket endpoint
```


## 📞 Support & Resources

### Discord Channels
- **[🗓┃𝖤𝗏𝖾𝗇𝗍𝗌-𝖠𝗇𝗇𝗈𝗎𝗇𝖼𝖾𝗆𝖾𝗇𝗍](https://discord.gg/jYeffuxs)**: Stay Active in the Announcement channel for Hackathon Update. 
- **[📝┃𝖤𝗏𝖾𝗇𝗍𝗌-𝖢𝗁𝖺𝗍](https://discord.gg/bHVKCYj4)**: Technical support.

### Reference Links
- [Hackathon Repository](https://github.com/iemafzalhassan/chattingo)
- [Docker Documentation](https://docs.docker.com/)
- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [Hostinger VPS Guide](https://www.hostinger.com/tutorials/vps)

