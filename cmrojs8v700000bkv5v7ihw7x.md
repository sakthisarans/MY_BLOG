---
title: "Getting Started with Kubernetes: Deploying Your First Minikube Container"
seoTitle: "Minikube Tutorial for Beginners"
seoDescription: "Learn to install Minikube with Docker, deploy your first Kubernetes app, and manage your local cluster with this beginner-friendly guide"
datePublished: 2024-11-15T10:30:00.000Z
cuid: cmrojs8v700000bkv5v7ihw7x
slug: getting-started-with-kubernetes-deploying-your-first-minikube-container
cover: https://cdn.hashnode.com/uploads/covers/69fb425450ecad45332f1a60/53484aa1-41fb-4f86-a711-c54b1f8731dc.jpg
ogImage: https://cdn.hashnode.com/uploads/og-images/69fb425450ecad45332f1a60/ad27bb70-14a9-4193-86fd-087f34c2b9cd.jpg
tags: docker, kubernetes

---

* * *

> Continuing our Kubernetes series, we move from understanding concepts to hands-on action! This guide focuses on setting up Minikube and deploying your first container in a Kubernetes environment. Following the foundational knowledge shared in our [Mastering Kubernetes: Core Concepts, Key Components, and Introduction to Minikube for Beginners](https://blog1.sakthisaran.in/introduction-to-kubernetes/), this tutorial will walk you through initializing Minikube, creating a deployment, and managing your containerized application easily. If you're eager to dive into Kubernetes practically, this post is your perfect starting point!

* * *

## Pre-Requirement for a Minikube

*   **Minikube Binary:** Download and install the minikube.exe from [minikube.sigs.k8s.io](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download)
    
*   **kubectl:** Install the kubectl by entering **minikube kubectl** in the terminal
    
*   **Docker:** Download and install docker from [Docker.com](https://www.docker.com/get-started/)
    

* * *

## Setting Up Minikube

### **Step 1: Verify Docker Installation**

*   To check if Docker is installed and running correctly:
    

```plaintext
docker --version
docker run hello-world
```

### **Step 2: Start Minikube with Docker Driver**

*   Run the following command to start Minikube using Docker as the driver:
    

```plaintext
minikube start --driver=docker
```

#### **Step 3: Verify Minikube Setup**

*   After the setup completes, verify that Minikube is running:
    

```plaintext
minikube status
```

*   To check cluster details:
    

```plaintext
kubectl get nodes
```

### Step 4: Interact with Minikube

*   You can now deploy your first containerized application. For example, to deploy a basic Nginx server:
    

```plaintext
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --type=NodePort --port=80
```

*   Get the service's URL:
    

```plaintext
minikube service nginx
```

*   The URL automatically opens in your browser to see the Nginx server running.
    

### Step 5: Deleting the Deployment

*   To delete the running deployment and service
    

```plaintext
kubectl delete deployment nginx
kubectl delete service nginx
```

### **Step 6: Managing Minikube**

*   To pause the cluster
    

```plaintext
minikube pause
```

*   To stop the cluster:
    

```plaintext
minikube stop
```

*   To delete the cluster:
    

```plaintext
minikube delete
```

* * *

## **Conclusion**

Setting up Minikube with Docker provides a lightweight and efficient way to explore Kubernetes on your local machine. With this setup, you’re now ready to dive deeper into container orchestration, deployments, and scaling. Stay tuned for more hands-on Kubernetes tutorials!