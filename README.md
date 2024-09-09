# Lab 1: Docker Tasks

## Overview

This lab focuses on Docker fundamentals, including building custom images, creating containers, understanding Docker networks, and setting up communication between containers.

---

## Lab Instructions

### P1: Create a Custom Nginx Docker Image (based on Ubuntu)
**Goal:**
- Build your own Nginx Docker image using `ubuntu` as the base image (do not use `nginx` as the base).

### All the following steps written in the docker file "Dockerfile"
  
**Steps:**
1. **Install Nginx:**
   - Start from the `ubuntu` image and install Nginx using `apt-get`.
  
2. **Add HTML Files:**
   - Create two HTML files:  
     - One as `index.html` in `/var/www/html/`.  
     - Another as a `.tar` file located at `/var/www/html/` containing an additional `index.html` file.
  
3. **Expose Port:**
   - Expose port `80` to allow access to Nginx from outside the container.
  
4. **Start Nginx:**
   - Ensure Nginx starts automatically when the container is run by using `CMD`.

**Commands:**
```bash
docker build . -t ubuntu/nginx
docker run -d --name Nginx -p 8080:80 ubuntu/nginx
```
![building ubuntu-nginx image](https://github.com/user-attachments/assets/d0aed6ca-9b4f-42a8-a131-d9d25f1b54f2)

### To get your ip you can get it by the following command on your host machine
```bash
ip a
```
### The output
![how to get your ip](https://github.com/user-attachments/assets/ba9d75e2-3500-451c-8386-49da195bce35)

### And by writing your ip:port on browser you should get the following default nginx page
![Nginx default page output](https://github.com/user-attachments/assets/aa4efad9-8973-48d0-bb0f-3d4d277fe70f)

---

### P2: Create a React App Docker Container (Single Stage Build)

**Goal:**
- Build and run a Docker container for a React application using a single-stage build process.

### All the following step written in the docker file "Reactapp"

**Steps:**
1. **Create React App:**
   - If you don’t have a React app, create one using the following command inside dockerfile:
     ```bash
      RUN npm create vite@latest my-app -- --template react
     ```

2. **Build and Run the Container:**
   - Build the Docker image with the following command: \
     I used the option `-f` because `docker build` by default search for dockerfile named "dockerfile" \
     In my case the dockerfile called "Reactapp" so i used `-f` to target the dockerfile
     
     ```bash
     docker build -f "Reactapp" -t odc-simple-reactapp .   
     ```
     
   - Run the container and map port `80` on your machine to port `3000` in the container:
     
     ```bash
     docker run  -p 80:3000 --name simple-reactapp -d odc-simple-reactapp
     ```
     
     ![run reactapp container](https://github.com/user-attachments/assets/e8fe3d9f-0693-4270-9e9b-64a7dee9ed34)
     
### And by writing your ip:port on browser you should get the following default vite+react page
![reactapp page output](https://github.com/user-attachments/assets/d3b82f65-a0fd-423b-9a82-f8251b74bbf4)

---

### P3: Docker Networks Overview
- You can find the definition of different network types of docker in `Docker Networks types.txt`

---

### P4: Custom Bridge Network with Container Communication

**Goal:**
- Create a custom bridge network and run two Ubuntu containers on it. Test communication between them using their names.

**Steps:**
1. **Create a Custom Bridge Network:**
   - Use Docker's `network create` command to create a custom bridge network:
     ```bash
     docker network create bridged-network
     ```
     ![list docker networks](https://github.com/user-attachments/assets/d969ef6e-0efc-4bbe-8a24-a438a5d0f958)

2. **Run Two Containers on the Custom Network:**
   - Start two Ubuntu containers with unique names on the custom network: \
     In my case i used an image on my host based on ubuntu `itvisionary-jenkins/terraform` and the image that we created in P1 `ubuntu/nginx`
     ```bash
     docker run  --name ubuntu1 --network my-bridge-network -it itvisionart-jenkins/terraform bash
     docker run  --name ubuntu2 --network my-bridge-network -it ubuntu/nginx bash
     ```
     ![list images and run ubuntu1 on created network](https://github.com/user-attachments/assets/9db0e9a5-c299-443a-a5ed-5f7c2d3d4bcf)
     ![run ubuntu2 on created network](https://github.com/user-attachments/assets/f05ce805-4d68-474b-81a0-da3f9571f9ad)

3. **ping command not found issue:**
   - Download package so you can use ping command if it is not downloaded by default in the base image: \
     In my case the base image of ubuntu wasn't have the ping command by default, run the following command in side container to be able using `ping` command
    ```bash
    apt-get install -y iputils-ping
    ```     

4. **Test Communication Between the Containers:**

   - Inside the container, ping the other container (`ubuntu2`) by its name: \
     In my case i pinged the first container from the second.
     ```bash
     ping ubuntu1
     ```

     ![ping ubuntu1 from ubunt2](https://github.com/user-attachments/assets/5ad101b1-de5e-4e98-8ffd-0cac343df6a9)

   - This confirms that the two containers can communicate over the custom bridge network using their container names.
