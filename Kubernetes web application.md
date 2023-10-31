### Kubernetes Project
In this project I am going to be deploying a simple web application into production environment using `Kubernetes`, `Dcker` and `MongoDB`
Various `Kubernetes` components like `Configmap` `Service` `Secret` and `Deployment` would  would be used for this deployment a prebuilt base `Docker` Image and a `MongoDB` endpoint for the web application database.

### Kubernetes WebApplication Refrences
For this project the following refrences would be needed in building and deploying my `Kubernetes` web application

`Kubernetes` official documentation: https://kubernetes.io/docs/home/

`Mongodb` image on Docker Hub: https://hub.docker.com/_/mongo

`webapp` image on Docker Hub: https://hub.docker.com/repository/docker/nanajanashia/k8s-demo-app

First thing I  did was to configure my `Kubernetes` components I started by creating a folder in my computer called `K8demo`
this folder conprised of diffrent files  like `mongo-config.yaml`,`mongo-secret.yaml` `mongo.yaml` and `webapp.yaml`

###Creating my local K8 Cluster

To crete my cluster I ensured to refrence `k8` documentation to get needed `k8` syntax to edit into what i want to create.

https://kubernetes.io/docs/home/

#Create `mongo-config.yaml`
Using the `k8` documentation, i created my `Config Map` called`mongo-config.yaml`
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongo-config
data:
  mongo-url: mongo-service
```

#Create `mongo-secret.yaml`
Using the `k8` documentation, i created my `secret` called`mongo-secret.yaml`
```
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secret
type: Opaque
data:
  mongo-user: bW9uZ291c2Vy
  mongo-password: bW9uZ29wYXNzd29yZA==
```
#Create `Deployment` `mongo.yaml`

Using the `k8` documentation, i created my `deployment` called`mongo.yaml`

Since the application im building is stateless i levereaged on `deployment`  and called it `mongo.yaml`
these are also the `blueprints`for the `pods` in my cluster so the configuration was a bit lengthy the syntax is below 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-deployment
  labels:
    app: mongo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
      - name: mongodb
        image: mongo:5.0
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-user
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-password  
---
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongo
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017
```
### Create `webapplication`

This is the part of our cluster that makes our application publicly accessible on the internet
I leveraged on the `k8` documentation and called `webapplication` `webapp.yaml` the sysntax is below 
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
  labels:
    app: webapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nanajanashia/k8s-demo-app:v1.0
        ports:
        - containerPort: 3000
        env:
        - name: USER_NAME
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-user
        - name: USER_PWD
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-password 
        - name: DB_URL
          valueFrom:
            configMapKeyRef:
              name: mongo-config
              key: mongo-url
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    app: webapp
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30100
```
### Create resources in cluster 

Now i have all the components of my web app ready I need to commit this syntax to my local cluster whis would host the application to do this i ran the following commands in Terminal 

First i created `Congif-map` in my cluster usuing the command 
```
Kubectl apply -f  mongo-congig.yaml
```
This automatically created `Congif-map` in my cluster 

Second was to create `Secret` in my cluster using the command 
```
Kubectl apply -f  mongo-secret.yaml
```
This automatically created `secret` in my cluster 

Third I created  `deployment` in my cluster using the command 
```
Kubectl apply -f  mongo.yaml
```
This automatically created `deployment`  and `service` in my cluster

Finally i created `Web-app` in my cluster using the command 
```
Kubectl apply -f  webapp.yaml
```
this automatically created `webapp deployment` and `webapp service`

### Verify all components have been successfully applied

I ran a few commands to ensure all my commits were successful to applied to my cluster 

```
Kubectl get all
```
This displayed all components in my cluster

### Access webbapp through browser
To access my web app through my browser i had to get the external ip adress of my node using the command 
```
kubectl get SVC
```
![Screenshot 2023-10-31 at 11 20 30](https://github.com/laolujr/Cloud-Projects-/assets/29700247/c638e596-aab8-486d-9144-20dbd10a4ebe)

I then used my node IP address to access my website in browser

