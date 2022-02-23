# k8s-hpa-php
A HorizontalPodAutoscaler (HPA for short) automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand.

## Folder structure
├── Dockerfile
├── hpa-php.yaml
└── index.php

## Pre-reqs
 To run the app and deployment, assumes the following dependencies/Pre-Requisites are met. If they are not installed and running as expected then please do follow the links and complete setting up the tools.

* [minikube](https://minikube.sigs.k8s.io/docs/start/) is installed and running 
    ```
    minikube status
    ```
* [docker](https://www.docker.com/) is installed and running
    ```sh
    docker version
    ````
* [kubectl](https://kubernetes.io/docs/tasks/tools/) is installed to interact with kubernetes cluster.
    ```sh
    kubectl version -o yaml 
    kubectl config view
    ```
    Note: Make sure kubectl  current-context is set to minikube
  
## How to use this 
  Run the folling commands 
  1. Start Minikube
     ```sh
      minikube start
      ```
      
  2. Docker build
      ```sh
    eval $(minikube -p minikube docker-env)
    docker image ls
    docker build --no-cache -t hap-example:latest .
    ```
 3. Deploy K8s manifest files which deploys a deployment with just 1 pod replica which 
    
        ```sh
        kubectl apply -f k8s-files/
        ```
 4. Create HPA 
    ```sh
    kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
    ```
 You can check the current status of the newly-made HorizontalPodAutoscaler, by running:

 You can use "hpa" or "horizontalpodautoscaler"; either name works OK.
 ```sh
  kubectl get hpa
  The output is similar to:

  NAME         REFERENCE                     TARGET    MINPODS   MAXPODS   REPLICAS   AGE
  php-apache   Deployment/php-apache/scale   0% / 50%  1         10        1          18s
  ```
  
### Testing Phase
  Increase the load by running the following command
  ```sh
  # Run this in a separate terminal
  # so that the load generation continues and you can carry on with the rest of the steps
  
  kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
  ```
  
  After a couple of minutes , run the following commands to see whether the pod count gone due to CPU usuage has gone up
  
  ```sh
  # type Ctrl+C to end the watch when you're ready
  kubectl get hpa php-apache --watch
  ```
  
  and also run to see number pods which should be more than 1
  
  ```sh
  kubectl get deployment php-apache
  ```
  
  

# Reference
  https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/
