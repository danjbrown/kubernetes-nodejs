# Node.js deployment with Docker and Kubernetes

An example of deploying a basic Node.js web application using Docker and Kubernetes, with Minikube and kubectl.

Kubernetes is a portable, extensible open-source platform for managing containerised workloads and services.

Minikube is a tool to run Kubernetes locally for development, providing a single-node Kubernetes cluster inside a VM on your machine.

kubectl is a command line tool for deploying and managing applications on Kubernetes.

1. Install Docker, minikube and kubectl, and start minikube and Docker
```
minikube start
```

2. Change the settings to build the image using the same Docker host as the Minikube VM
```
eval $(minikube docker-env) 
```

3. Clone the repository and build v1 of the Docker image 
``` 
docker build -t kubernetes-nodejs:v1 .
```
Note: the Docker image can be run manually by going into the application directory (where the Dockerfile is located) and running this command
```
docker run -p 49160:8080 -d kubernetes-nodejs
```

4. Create the deployment and load balancer service on Kubernetes using the configuration files in the /kubernetes directory. The Node.js app runs by default on port 8080 which is mapped to the externally accessible port 30001 using the load balancer service.
```
kubectl apply -f kubernetes
```
Note: it is also possible create the deployment and load balancer service manually on the command line
```
kubectl run kubernetes-nodejs --image=kubernetes-nodejs:v1 --port=8080 --image-pull-policy=Never
kubectl expose deployment kubernetes-nodejs --type=LoadBalancer
```

5. View the services, deployments and pods 
```
kubectl get services
kubectl get deploymemts
kubectl get pods
```

6. Start the Minikube dashboard in a browser
```
minikube dashboard
```

7. Start the Minikube service which opens the deployed application in a browser
```
minikube service kubernetes-nodejs
```

8. Update the application
Change the server.js file to create some new output then build and deploy v2
```
docker build -t kubernetes-nodejs:v2 .
kubectl set image deployment/kubernetes-nodejs kubernetes-nodejs=kubernetes-nodejs:v2
minikube service kubernetes-nodejs
```
The changes should be loaded in the browser

9. Clean up the application
Delete the Kubernetes deployment and service, and Docker images
```
kubectl delete -f kubernetes/
docker rmi kubernetes-nodejs:v1 kubernetes-nodejs:v2 -f
minikube stop
eval $(minikube docker-env -u)
minikube delete
```

Notes:
Scale the number of pods
```
kubectl scale -f kubernetes/deployment.yml --replicas=3
```