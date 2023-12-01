## Create a deployment: Nginx 1.14  with 4 replicats 

## nginx-deployment.yaml 
 apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx 
  name: nginx-deployment
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: web-server
        image: nginx:1.14
        ports:
        - containerPort: 80

## ## commande: kubectl apply -f nginx-deployment.yaml

## Access this deployment with
# NodePort Service
# ClusterIp service

## service-node.yaml
apiVersion: v1
kind: Service
metadata:
  name: service-node
spec:
  type: NodePort
  ports:
  - nodePort: 32000
    port: 8080
    targetPort: 80
  selector:
    app: nginx

## ## -commande: kubectl apply -f service-node.yaml

## service-cluster.yaml
apiVersion: v1
kind: Service
metadata:
  name: service-cluster
spec:
  ports:
    - port: 80
      protocol: TCP
  selector: 
    app: nginx


## ##-commande: kubectl apply -f service-cluster.yaml

# => Maintenant ajouter ce commande pour coonecter a votre port
kubectl get nodes -o wide

internelip+nodePort => ajouter a votre browser



## Update the Nginx version to 1.16 then watch the update process with
# RollingUpdate strategy with 50% of replicats

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx 
  name: nginx-deployment
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: web-server
        image: nginx:1.16
        ports:
        - containerPort: 80
        startupProbe:
            tcpSocket:
              port: 80
            initialDelaySeconds: 10
            periodSeconds: 5

#### - commande 1 : kubectl apply -f nginx-deployment.yaml
# Recreate strategy

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx 
  name: nginx-deployment
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: web-server
        image: nginx:1.16
        ports:
        - containerPort: 80
        startupProbe:
            tcpSocket:
              port: 80
            initialDelaySeconds: 10
            periodSeconds: 5
    

# describe your pod
kubectl describe pods

# => pour connaitre la difference entre eux sisir cette commande aprés le changement 

watch -n 0.5 kubectl get pods -n default