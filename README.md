# reddit-clone
Delivery of Highly Scalable APP like "Reddit"using Docker, Docker Hub, Kubernetes and Minikube. Auto healing and scaling is obtained.

Step 1: Clone the source code
https://github.com/LondheShubham153/reddit-clone-k8s-ingress.git

Step 2: Containerize the Application using Docker
Write a Dockerfile with following code:
FROM node:19-alpine3.15

WORKDIR /reddit-clone

COPY . /reddit-clone

RUN npm install 

EXPOSE 3000

CMD ["npm","run","dev"]


Step 3) Building Docker-Image
docker build -t <DockerHub_Username>/<Imagename> . use this command to build a docker image.

Step 4) login to your DockerHub account using Command i.e "docker login" and give your username & password.

Step 5) Push the Image To DockerHub
use "docker push <DockerHub_Username>/<Imagename>" for pushing to the DockerHub.
You can use an existing docker image i.e khushalchaudhari23/test for deployment.

Step 6) Write a Kubernetes Manifest File,which is "Deployment.yml file"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: reddit-clone-deployment
  labels:
    app: reddit-clone
spec:
  replicas: 2
  selector:
    matchLabels:
      app: reddit-clone
  template:
    metadata:
      labels:
        app: reddit-clone
    spec:
      containers:
      - name: reddit-clone
        image: trainwithshubham/reddit-clone
        ports:
        - containerPort: 3000
Step 7) Write Service.yml file
apiVersion: v1
kind: Service
metadata:
  name: reddit-clone-service
  labels:
    app: reddit-clone
spec:
  type: NodePort
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 31000
  selector:
    app: reddit-clone
Step 8) Deploy the app to Kubernetes & Create a Service For It
Now, we have a deployment file for our app and we have a running Kubernetes cluster, we can deploy the app to Kubernetes. To deploy the app you need to run following the command: kubectl apply -f Deployment.yml Just Like this create a Service using kubectl apply -f Service.yml

If You want to check your deployment & Service use the command kubectl get deployment & kubectl get services

Step 9) Let's Configure Ingress :ingress.yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-reddit-app
spec:
  rules:
  - host: "domain.com"
    http:
      paths:
      - pathType: Prefix
        path: "/test"
        backend:
          service:
            name: reddit-clone-service
            port:
              number: 3000
  - host: "*.domain.com"
    http:
      paths:
      - pathType: Prefix
        path: "/test"
        backend:
          service:
            name: reddit-clone-service
            port:
              number: 3000
        
Step 10) Now you able to create ingress for your service. kubectl apply -f ingress.yml use this command to apply ingress settings.
Verify that the ingress resource is running correctly by using kubectl get ingress ingress-reddit-app command.

Step 11) Expose the app
First We need to expose our deployment so use kubectl expose deployment reddit-clone-deployment --type=NodePort command.
You can test your deployment using curl -L http://192.168.49.2:31000. 192.168.49.2 is a minikube ip & Port 31000 is defined in Service.yml
Then We have to expose our app service kubectl port-forward svc/reddit-clone-service 3000:3000 --address 0.0.0.0 &

Step 12)
Test Ingress
Now It's time to test your ingress so use the curl -L domain/test command in the terminal.
You can also see the deployed application on Ec2_ip:3000
Note:- Make sure you open the 3000 port in a security group of your Ec2 Instance.



Cheeeeearsssss::::::::You have successfully Deployed a Reddit Copy on Kubernetes with Ingress Enabled
