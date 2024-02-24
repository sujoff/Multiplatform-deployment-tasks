# Janus and EMQ X Setup

This README covers setting up Janus and EMQ X messaging brokers in Docker and Kubernetes.

## Janus in Docker

1. Pull the official Janus Docker image:

    ```bash
    docker pull meetecho/janus-gateway:latest
    ```

2. Run the Janus container, exposing port 8088 and mounting a volume for recordings:

    ```bash 
    docker run -p 8088:8088 -v /path/to/recordings:/opt/janus/var/janus/recordings meetecho/janus-gateway
    ```

3. Janus should now be available on port 8088 on your Docker host.

## EMQ X in Docker

1. Pull the official EMQ X Docker image:

    ```bash
    docker pull emqx/emqx:latest
    ```

2. Run the EMQ X container, exposing ports for MQTT, WebSocket and dashboard:

    ```bash
    docker run -p 1883:1883 -p 8083:8083 -p 8084:8084 -p 8883:8883 emqx/emqx
    ```

3. EMQ X should now be available on ports 1883 (MQTT), 8083 (WebSocket), 8084 (dashboard) on your Docker host.

## Janus in Kubernetes

1. Create a Janus deployment and service YAML file (e.g. janus.yaml):

    ```yaml
    # Deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: janus
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: janus
      template:
        metadata:
          labels:
            app: janus
        spec:
          containers:
          - name: janus
            image: meetecho/janus-gateway
            ports:
            - containerPort: 8088

    ---

    # Service  
    apiVersion: v1
    kind: Service
    metadata:
      name: janus
    spec:
      type: LoadBalancer
      ports:
      - port: 8088
        targetPort: 8088
      selector:
        app: janus
    ```

2. Apply the YAML file:

    ```
    kubectl apply -f janus.yaml
    ```

3. Janus should now be available through the service's load balancer IP.

## EMQ X in Kubernetes

1. Create an EMQ X deployment and service YAML file (e.g. emqx.yaml):

    ```yaml
    # Deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: emqx
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: emqx
      template:
        metadata:
          labels:
            app: emqx
        spec:
          containers:
          - name: emqx
            image: emqx/emqx:latest
            ports:
            - containerPort: 1883
            - containerPort: 8083
            - containerPort: 8084
            - containerPort: 8883

    ---

    # Service
    apiVersion: v1 
    kind: Service
    metadata:  
      name: emqx
    spec:
      type: LoadBalancer
      ports:
      - port: 1883
        targetPort: 1883
      - port: 8083  
        targetPort: 8083
      - port: 8084
        targetPort: 8084
      - port: 8883
        targetPort: 8883
      selector:
        app: emqx
    ```

2. Apply the YAML file:

    ```
    kubectl apply -f emqx.yaml
    ```

3. EMQ X should now be available through the service's load balancer IP.
