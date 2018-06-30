# Node.js deployment with Docker and Kubernetes

An example of deploying a basic Node.js web application using Docker and Kubernetes, with minikube and kubectl.

Minikube is a tool to run Kubernetes locally for development, providing a single-node Kubernetes cluster inside a VM on your machine.

kubectl is a command line tool for deploying and managing applications on Kubernetes.

1. Install Docker, minikube and kubectl and start minikube
```
minikube start
```
Ensure Docker is running locally

2. Change the settings to build the image using the same Docker host as the Minikube VM
```
eval $(minikube docker-env) 
```
Note: this can be reverted later using
```
 eval $(minikube docker-env -u)
```

3. Clone the repository then build v1 of the Docker image 
``` 
docker build -t kubernetes-nodejs:v1 .
```
Note: you can run the Docker image manually by going into the application directory (where the Docker file is located) and running this command
```
docker run -p 49160:8080 -d kubernetes-nodejs
```

4. Create the deployment and load balancer service on Kubernetes using the configuration files in the /kubernetes directory. As you can see in these files port 8080 on which the Node.js app runs is mapped to the external port 30001 using the LoadBalancer service.
```
kubectl apply -f kubernetes
```
It is also possible create the deployment and load balancer service manually on the command line
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

6. Start the minikube dashboard in a browser
```
minikube dashboard
```

7. Start the minikube service and open the deployed application in a browser
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