# YAML Basics
---------------------

## Step-01: Comments & Key Value Pairs
- Space after colon is mandatory to differentiate key and value
```yml
# Defining simple key value pairs
name: Rana
age: 23
city: Hyderabad
```

## Step-02: Dictionary / Map
- Set of properties grouped together after an item
- Equal amount of blank space required for all the items under a dictionary
```yml
person:
  name: Rana
  age: 23
  city: Hyderabad
```

## Step-03: Array / Lists
- Dash indicates an element of an array
```yml
person: # Dictionary
  name: Rana
  age: 23
  city: Hyderabad
  hobbies: # List  
    - cycling
    - cookines
  hobbies: [cycling, cooking]   # List with a differnt notation  
```  

## Step-04: Multiple Lists
- Dash indicates an element of an array
```yml
person: # Dictionary
  name: Rana
  age: 23
  city: Hyderabad
  hobbies: # List  
    - cycling
    - cooking
  hobbies: [cycling, cooking]   # List with a differnt notation  
  friends: # 
    - name: friend1
      age: 22
    - name: friend2
      age: 25            
```  


## Step-05: Sample Pod Tempalte for Reference
```yml
apiVersion: v1 # String
kind: Pod  # String
metadata: # Dictionary
  name: myapp-pod
  labels: # Dictionary 
    app: myapp         
spec:
  containers: # List
    - name: myapp
      image: stacksimplify/kubenginx:1.0.0
      ports:
        - containerPort: 80
          protocol: "TCP"
        - containerPort: 81
          protocol: "TCP"
```



# PODs with YAML
## Step-01: Kubernetes YAML Top level Objects
- Discuss about the k8s YAML top level objects
- **01-kube-base-definition.yml**
```yml
apiVersion:
kind:
metadata:
  
spec:
```
-  **Pod API Objects Reference:**  https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#pod-v1-core

## Step-02: Create Simple Pod Definition using YAML 
- We are going to create a very basic pod definition
- **02-pod-definition.yml**
```yml
apiVersion: v1 # String
kind: Pod  # String
metadata: # Dictionary
  name: myapp-pod
  labels: # Dictionary 
    app: myapp         
spec:
  containers: # List
    - name: myapp
      image: stacksimplify/kubenginx:1.0.0
      ports:
        - containerPort: 80
```
- **Create Pod**
```
# Create Pod
kubectl create -f 02-pod-definition.yml
[or]
kubectl apply -f 02-pod-definition.yml

# List Pods
kubectl get pods
```

## Step-03: Create a NodePort Service
- **03-pod-nodeport-service.yml**
```yml
apiVersion: v1
kind: Service
metadata:
  name: myapp-pod-nodeport-service  # Name of the Service
spec:
  type: NodePort
  selector:
  # Loadbalance traffic across Pods matching this label selector
    app: myapp
  # Accept traffic sent to port 80    
  ports: 
    - name: http
      port: 80    # Service Port
      targetPort: 80 # Container Port
      nodePort: 31231 # NodePort
```
- **Create NodePort Service for Pod**
```
# Create Service
kubectl apply -f 03-pod-nodeport-service.yml

# List Service
kubectl get svc

# Get Public IP
kubectl get nodes -o wide

# Access Application
http://<WorkerNode-Public-IP>:<NodePort>
http://<WorkerNode-Public-IP>:31231
```

## API Object References
-  **Pod**: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#pod-v1-core
- **Service**: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#service-v1-core

## Updated API Object References
-  **Pod**: https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/
-  **Service**: https://kubernetes.io/docs/reference/kubernetes-api/service-resources/service-v1/
- **Kubernetes API Reference:** https://kubernetes.io/docs/reference/kubernetes-api/


# ReplicaSets with YAML
-------------------------

## Step-01: Create ReplicaSet Definition
- **replicaset-definition.yml**
```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp2-rs
spec:
  replicas: 3 # 3 Pods should exist at all times.
  selector:  # Pods label should be defined in ReplicaSet label selector
    matchLabels:
      app: myapp2
  template:
    metadata:
      name: myapp2-pod
      labels:
        app: myapp2 # Atleast 1 Pod label should match with ReplicaSet Label Selector
    spec:
      containers:
      - name: myapp2
        image: stacksimplify/kubenginx:2.0.0
        ports:
          - containerPort: 80
```
## Step-02: Create ReplicaSet
- Create ReplicaSet with 3 Replicas
```
# Create ReplicaSet
kubectl apply -f 02-replicaset-definition.yml

# List Replicasets
kubectl get rs
```
- Delete a pod
- ReplicaSet immediately creates the pod. 
```
# List Pods
kubectl get pods

# Delete Pod
kubectl delete pod <Pod-Name>
```

## Step-03: Create NodePort Service for ReplicaSet
```yml
apiVersion: v1
kind: Service
metadata:
  name: replicaset-nodeport-service
spec:
  type: NodePort
  selector:
    app: myapp2
  ports:
    - name: http
      port: 80
      targetPort: 80
      nodePort: 31232  
```
- Create NodePort Service for ReplicaSet & Test
```
# Create NodePort Service
kubectl apply -f 03-replicaset-nodeport-servie.yml

# List NodePort Service
kubectl get svc

# Get Public IP
kubectl get nodes -o wide

# Access Application
http://<Worker-Node-Public-IP>:<NodePort>
http://<Worker-Node-Public-IP>:31232

```

## API References
- **ReplicaSet:** https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#replicaset-v1-apps


# Deployments with YAML
---------------------------------

## Step-01: Copy templates from ReplicaSet
- Copy templates from ReplicaSet and change the `kind: Deployment` 
- Update Container Image version to `3.0.0`
- Update NodePort service `nodePort: 31233`
- Change all names to Deployment
- Change all labels and selectors to `myapp3`

```
# Create Deployment
kubectl apply -f 02-deployment-definition.yml
kubectl get deploy
kubectl get rs
kubectl get po

# Create NodePort Service
kubectl apply -f 03-deployment-nodeport-service.yml

# List Service
kubectl get svc

# Get Public IP
kubectl get nodes -o wide

# Access Application
http://<Worker-Node-Public-IP>:31233
```
## API References
- **Deployment:** https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#deployment-v1-apps
