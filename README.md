# Java/Spring Boot + Kubernetes Sandbox

## Objective
This project is meant to be a "dummy" Spring Boot application that stands up a single REST API and can be used to test
containerization and deployment to a Kubernetes cluster.

The primary motivation behind this was to have a simple app to deploy to a local minikube cluster to practice configuring
and monitoring in preparation for the CKAD exam.

## Prerequisites

 - Standard Java development/build tools (JDK 8, Maven)
 - `docker-cli` and `kubectl` for building container image and interacting with k8s cluster
   - You will also need Docker desktop and minikube if you plan to do testing locally

## Building/Deploying/Testing

### Building the Application Container Image

 1. From the project root directory, run `mvn clean package`
 2. If you are planning to deploy to a local cluster via minikube and don't want to push your image to Docker Hub, 
follow the steps below (source: https://stackoverflow.com/a/42564211). Otherwise, skip to step 3
    * eval $(minikube docker-env)
    * make sure your `imagePullPolicy` is set to `Never` in the deployment.yml file
 3. Build the container image via `docker build -t <IMAGE_NAME> .`
 4. If pushing to Docker Hub, do so via `docker push <IMAGE_NAME>`

### Deploying to Kubernetes

 1. Ensure kubectl is pointing to the target cluster (`kubectl config current-context`)
 2. Create your deployment via `kubectl apply -f kube/deployment.yml`
    * You will need to update the image name in the `spec.template.spec.containers.image` field in deployment.yml to match
    your image name
 3. Run `kubectl get pod` and ensure your application pods are up and running
 4. Expose your pods with a service using `kubectl expose deploy spring-kuberized --port=80 --targetPort=8080`
 5. Send an HTTP request to <TARGET_HOST_OR_IP>/api/hello and validate that you can see a response (whichever Pod served
    your request will also have a message printed out in the container logs)
    * If you are using a local installation of minikube, you will have to run `minikube service <SERVICE_NAME> --url`
    which will provide you with a base URL upon which you can append the path (/api/hello)

### Reference Documentation
For further reference, please consider the following sections:

* [Spring Initializer](https://start.spring.io)