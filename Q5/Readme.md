# MicroK8s Single Node Setup with Nginx

This README file is for creating single node MicroK8s cluster and deploying Nginx to it as a part of assignment.

## Setup MicroK8s

1. Install MicroK8s on the node:

    ```bash
    sudo snap install microk8s --classic
    ```

2. Enable the DNS and storage addons:

    ```bash
    microk8s enable dns storage
    ```

3. Verify MicroK8s is running:

    ```bash
    microk8s status
    ```

## Deploy Nginx

1. Create a deployment and service for Nginx:

    ```bash
    microk8s kubectl create deployment nginx --image=nginx
    microk8s kubectl expose deployment nginx --port=80 --type=NodePort
    ```

2. Get the node port that Nginx is exposed on:

    ```bash
    nginx_port=$(microk8s kubectl get svc nginx -o go-template='{{(index .spec.ports 0).nodePort}}')
    ```

3. Access the Nginx server:

    ```bash
    curl localhost:$nginx_port
    ```

You should see the Nginx welcome page returned.

