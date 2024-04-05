Challenge 1: Write a deployment file and run the simple react static website on Kubernetes with 5 replicas. Host a simple static website as its domain name should be example.com using ingress and Nginx on Kubernetes.

----
# Deploying a Simple React Static Website on Kubernetes

This project demonstrates how to deploy a React static website with 5 replicas on a Kubernetes cluster and configure it to be accessible at `example.com` using an Ingress with Nginx.

**Prerequisites:**

- Basic understanding of React, Docker, and Kubernetes concepts
- A functional Kubernetes cluster (e.g., Minikube, Docker Desktop with Kubernetes)
- `kubectl` command-line tool configured to interact with your Kubernetes cluster

**Steps:**

1. **Create a React Static Website**

   - If you don't have one, use `create-react-app` to generate a basic React project:

     ```bash
     npx create-react-app my-react-app
     ```

   - Develop your React application's components and styles within the `src` directory.

2. **Build the Production Build**

   - Navigate to your project directory:

     ```bash
     cd my-react-app
     ```

   - Build the production-optimized version of your React app:

     ```bash
     npm run build
     ```

   - This creates a `build` directory containing all the static files needed for deployment.

3. **Create a Dockerfile**

   - In the project root directory, create a file named `Dockerfile` with the following content:

     ```dockerfile
     FROM node:16-alpine AS builder
     WORKDIR /app

     # Copy package.json and package-lock.json (or yarn.lock)
     COPY package*.json ./
     RUN npm install

     # Copy all project files
     COPY . .

     # Build the React app for production
     RUN npm run build

     # Switch to Nginx image for serving static files
     FROM nginx:alpine

     # Copy the built files from the previous stage
     COPY --from=builder /app/build /usr/share/nginx/html

     # Expose port 80 for web traffic
     EXPOSE 80

     # Start Nginx in the foreground
     CMD ["nginx", "-g", "daemon off;"]
     ```

   - This Dockerfile builds a multi-stage image, as explained in the comments within the file.

4. **Build and Push the Docker Image**

   - Build the Docker image:

     ```bash
     docker build -t my-react-app:latest .
     ```

     - Replace `my-react-app` with your desired image name if needed.

   - Push the image to a Docker registry (e.g., Docker Hub) if you plan to deploy to a remote cluster:

     ```bash
     docker login  # Log in to your Docker registry
     docker push my-react-app:latest
     ```

5. **Create Deployment YAML File**

   - In the project root directory, create a file named `deployment.yaml` with the following content:

     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: my-react-app
     spec:
       replicas: 5  # Deploy 5 replicas of the application
       selector:
         matchLabels:
           app: my-react-app
       template:
         metadata:
           labels:
             app: my-react-app
         spec:
           containers:
           - name: my-react-app
             image: my-react-app:latest  # Replace with your image name/registry if needed
             ports:
             - containerPort: 80
     ```

   - This configuration file defines the deployment settings, including the number of replicas and container details.

6. **Create Ingress YAML File**

   - Create a file named `ingress.yaml` with the following content:

     ```yaml
     apiVersion: networking.k8s.io/v1
     kind: Ingress
     metadata:
       name: my-react-app-ingress
     spec:
       rules:  # Complete this section with your desired rules
                 # (e.g., hostname, path) for accessing the application
     ```

   - This file defines the Ingress configuration, specifying how to route traffic to your application. You'll need to complete the `rules` section with the appropriate hostname and/or path for accessing your application at `example.com`. Refer to the Kubernetes documentation for Ingress rules: https://kubernetes.io/docs/concepts/services-networking/ingress/

**Deployment:**

1. Apply the deployment and ingress configurations:

   ```bash
   kubectl apply -f deployment.

