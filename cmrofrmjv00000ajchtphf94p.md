---
title: "From Zero to Docker: A Beginner’s Journey"
seoTitle: "From Zero to Docker: A Beginner’s Journey"
seoDescription: "Learn Docker from scratch with this beginner-friendly guide covering installation, Dockerfiles, containers, images, and Docker Hub."
datePublished: 2024-10-23T10:30:00.000Z
cuid: cmrofrmjv00000ajchtphf94p
slug: from-zero-to-docker-a-beginner-s-journey
cover: https://cdn.hashnode.com/uploads/covers/69fb425450ecad45332f1a60/34fa72fd-fe63-4982-8070-8e8cd4cf3b4b.webp
ogImage: https://cdn.hashnode.com/uploads/og-images/69fb425450ecad45332f1a60/693dae67-75b5-4f30-a3a0-4f06d0f16c26.webp

---

* * *

> In today’s fast-paced development environment, deploying applications efficiently and consistently across various systems is a key challenge. **Docker** has revolutionized this space by enabling developers to package applications with all their dependencies into a container that runs uniformly on any platform. This blog will provide a beginner-friendly introduction to Docker and Docker Hub, including a technical walkthrough of setting them up and using them for your projects.

## **What is Docker?**

Docker is a platform that allows you to build, deploy, and manage applications in isolated environments called containers. Containers are lightweight, portable, and run consistently regardless of the host environment, making them ideal for both development and production setups.

## **Why Docker?**

Docker is lightweight because it doesn't containerize the entire operating system; instead, it packages only the application and its dependencies. By sharing the host system's kernel, Docker significantly reduces CPU and memory usage compared to traditional virtualization tools like VirtualBox or VMware, which require full OS virtualization. This efficient resource use makes Docker an ideal choice for running applications with minimal overhead.

## **Key Benefits of Docker:**

1.  **Isolation**: Each Docker container runs its environment, ensuring that there’s no conflict between different applications and their dependencies.
    
2.  **Portability**: Containers run the same way on different environments, making it easier to move applications between development, testing, and production.
    
3.  **Efficiency**: Docker containers use fewer resources compared to traditional virtual machines since they share the same operating system kernel.
    

* * *

## **What is Docker Hub?**

**Docker Hub** is a cloud-based registry that lets you store, share, and manage Docker images. It’s a centralized repository where you can find pre-built images, or you can upload your own for personal or public use.

*   **Public repositories** allow anyone to pull and use your Docker images.
    
*   **Private repositories** allow you to control access, making it suitable for sensitive or proprietary applications.
    

## **Setting Up Docker: A Step-by-Step Guide**

### **1\. Install Docker**

Docker supports multiple platforms, including Linux, Windows, and macOS. Follow the below steps for installing Docker on your machine.

*   **For Windows and macOS**: Download and install **Docker Desktop** from the [Docker website](https://www.docker.com/products/docker-desktop/). Docker Desktop includes everything you need to start building and running containers.
    

#### Important Note:

For Windows, it’s crucial to enable wsl2 and hypervisor platform from turn Windows feature panel to run the docker desktop

*   **For Linux**: Use your package manager. For example, on Ubuntu:
    

```plaintext
sudo apt update
sudo apt install docker.io
```

Once installed, verify the installation by running:

```plaintext
docker --version
```

### **2\. Basic Docker Commands**

Now that Docker is installed, let’s run some basic commands.

**Check Docker installation:**

```plaintext
docker --version
```

**Run a basic Docker container:**

```plaintext
docker run hello-world
```

*   This command downloads and runs the **hello-world** image from Docker Hub, verifying that Docker is working correctly.
    

**List running containers:**

```plaintext
docker ps
```

Use this command to see the running containers (For all Containers)

```plaintext
docker ps -a
```

### **3\. Creating a Docker Image**

To create a Docker container with your application, you first need to define a Dockerfile.

**Create a Dockerfile:**

```plaintext
# Use Node.js runtime as a parent image.
FROM node:14

# Need to set a working directory in the container
WORKDIR /usr/src/app

# Copy current directory contents into a container
COPY . .

# Install any needed dependencies
RUN npm install

# Make port 8080 available to the world outside the container
EXPOSE 8080

# Define the command to run your app
CMD ["node", "app.js"]
```

This Dockerfile does the following:

*   Specifies the base image as **Node.js 14**.
    
*   Copies your project files into the container.
    
*   Install dependencies using npm install.
    
*   Exposes port 8080 for your app to communicate with the outside world.
    
*   Runs the application using node app.js.
    

**Build the Docker image:**

```plaintext
docker build -t my-node-app:latest .
```

The -t flag names the image **my-node-app**.

The tag after the colleen specifies the container version.

The . represents the location of the dockerfile, here it represents the current directory.

### **4\. Running a Docker Container**

Now, once the image is built, run it as a container using:

```plaintext
docker run -p 8080:8080 —-env=port=8080 —-volume=C://user/home:/data my-node-app
```

The -p option maps the container’s port 8080 to your machine’s port 8080, allowing you to access your application from the browser at http://localhost:8080.

The --env sets the environmental variables that are required for the application to run

The --volume mounts the host system file directory to the containers file directory in this case C://user/home directory of the host is mounted to the /data directory of the container

### **5\. Pushing to Docker Hub**

If you need to share your Docker image, then you can push it to **Docker Hub**.

1.  **Create a Docker Hub account** if you don’t have one already at [hub.docker.com](https://hub.docker.com/). **Log in to Docker Hub** from your terminal:
    

```plaintext
docker login
```

2.  **Tag your Docker image** with your Docker Hub username:
    

```plaintext
docker tag my-node-app your-dockerhub-username/my-node-app
```

3.  **Push the image to Docker Hub**:
    

```plaintext
docker push your-dockerhub-username/my-node-app
```

4.  Now, your Docker image is publicly available on Docker Hub and can be pulled by anyone with the following command:
    

```plaintext
docker pull your-dockerhub-username/my-node-app
```

* * *

## **Practical Use Cases of Docker**

Docker isn’t just for developers; it’s used across various scenarios:

*   **Development and Testing**: Developers can create containers that mirror production environments, ensuring that what works on their machine will also work in production.
    
*   **CI/CD Pipelines**: Docker allows you to create consistent environments across the development lifecycle, integrating seamlessly into continuous integration/continuous delivery pipelines.
    
*   **Microservices Architecture**: Docker makes deploying microservices easier, allowing each service to run in its containerized environment.
    

* * *

## **Conclusion**

Docker, combined with Docker Hub, simplifies application deployment and environment consistency across various stages of development. By containerizing your applications, you ensure they can run anywhere, making development, testing, and deployment much smoother. Whether you’re a beginner just getting started or a seasoned developer, Docker is a powerful tool to integrate into your workflow.