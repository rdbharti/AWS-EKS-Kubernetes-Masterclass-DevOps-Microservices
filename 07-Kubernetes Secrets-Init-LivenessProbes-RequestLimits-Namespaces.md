# Kubernetes - Secrets
------------------------------

## Step-01: Introduction
- Kubernetes Secrets let you store and manage sensitive information, such as passwords, OAuth tokens, and ssh keys. 
- Storing confidential information in a Secret is safer and more flexible than putting it directly in a Pod definition or in a container image. 

## Step-02: Create Secret for MySQL DB Password
### 
```
# Mac
echo -n 'dbpassword11' | base64

# URL: https://www.base64encode.org
```
### Create Kubernetes Secrets manifest
```yml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-db-password
#type: Opaque means that from kubernetes's point of view the contents of this Secret is unstructured.
#It can contain arbitrary key-value pairs. 
type: Opaque
data:
  # Output of echo -n 'dbpassword11' | base64
  db-password: ZGJwYXNzd29yZDEx
```
## Step-03: Update secret in MySQL Deployment for DB Password
```yml
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-db-password
                  key: db-password
```

## Step-04: Update secret in UMS Deployment
- UMS means User Management Microservice
```yml
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-db-password
                  key: db-password
```

## Step-05: Create & Test
```
# Create All Objects
kubectl apply -f kube-manifests/

# List Pods
kubectl get pods

# Access Application Health Status Page
http://<WorkerNode-Public-IP>:31231/usermgmt/health-status
```

## Step-06: Clean-Up
- Delete all k8s objects created as part of this section
```
# Delete All
kubectl delete -f kube-manifests/

# List Pods
kubectl get pods

# Verify sc, pvc, pv
kubectl get sc,pvc,pv
```

# Kubernetes - Init Containers
--------------------------------

## Step-01: Introduction
- Init Containers run **before** App containers
- Init containers can contain **utilities or setup scripts** not present in an app image.
- We can have and run **multiple Init Containers** before App Container. 
- Init containers are exactly like regular containers, **except:**
  - Init containers always **run to completion.**
  - Each init container must **complete successfully** before the next one starts.
- If a Pod's init container fails, Kubernetes repeatedly restarts the Pod until the init container succeeds.
- However, if the Pod has a `restartPolicy of Never`, Kubernetes does not restart the Pod.


## Step-02: Implement Init Containers
- Update `initContainers` section under Pod Template Spec which is `spec.template.spec` in a Deployment
```yml
  template:
    metadata:
      labels:
        app: usermgmt-restapp
    spec:
      initContainers:
        - name: init-db
          image: busybox:1.31
          command: ['sh', '-c', 'echo -e "Checking for the availability of MySQL Server deployment"; while ! nc -z mysql 3306; do sleep 1; printf "-"; done; echo -e "  >> MySQL DB Server has started";']
```


## Step-03: Create & Test
```
# Create All Objects
kubectl apply -f kube-manifests/

# List Pods
kubectl get pods

# Watch List Pods screen
kubectl get pods -w

# Describe Pod & Discuss about init container
kubectl describe pod <usermgmt-microservice-xxxxxx>

# Access Application Health Status Page
http://<WorkerNode-Public-IP>:31231/usermgmt/health-status
```

## Step-04: Clean-Up
- Delete all k8s objects created as part of this section
```
# Delete All
kubectl delete -f kube-manifests/

# List Pods
kubectl get pods

# Verify sc, pvc, pv
kubectl get sc,pvc,pv
```

## References:
- https://kubernetes.io/docs/concepts/workloads/pods/init-containers/


# Kubernetes - Liveness & Readiness Probes
-------------------------------------------

## Step-01: Introduction
- Refer `Probes` slide for additional details

## Step-02: Create Liveness Probe with Command
```yml
          livenessProbe:
            exec:
              command:
                - /bin/sh
                - -c
                - nc -z localhost 8095
            initialDelaySeconds: 60
            periodSeconds: 10
```

## Step-03: Create Readiness Probe with HTTP GET
```yml
          readinessProbe:
            httpGet:
              path: /usermgmt/health-status
              port: 8095
            initialDelaySeconds: 60
            periodSeconds: 10     
```


## Step-04: Create k8s objects & Test
```
# Create All Objects
kubectl apply -f kube-manifests/

# List Pods
kubectl get pods

# Watch List Pods screen
kubectl get pods -w

# Describe Pod & Discuss about init container
kubectl describe pod <usermgmt-microservice-xxxxxx>

# Access Application Health Status Page
http://<WorkerNode-Public-IP>:31231/usermgmt/health-status
```
- **Observation:** User Management Microservice pod witll not be in READY state to accept traffic until it completes the `initialDelaySeconds=60seconds`. 

## Step-05: Clean-Up
- Delete all k8s objects created as part of this section
```
# Delete All
kubectl delete -f kube-manifests/

# List Pods
kubectl get pods

# Verify sc, pvc, pv
kubectl get sc,pvc,pv
```


## References:
- https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/


# Kubernetes - Requests and Limits
-----------------------------------

## Step-01: Introduction
- We can specify how much each container a pod needs the resources like CPU & Memory. 
- When we provide this information in our pod, the scheduler uses this information to decide which node to place the Pod on. 
- When you specify a resource limit for a Container, the kubelet enforces those `limits` so that the running container is not allowed to use more of that resource than the limit you set. 
-  The kubelet also reserves at least the `request` amount of that system resource specifically for that container to use.

## Step-02: Add Requests & Limits
```yml
          resources:
            requests:
              memory: "128Mi" # 128 MebiByte is equal to 135 Megabyte (MB)
              cpu: "500m" # `m` means milliCPU
            limits:
              memory: "500Mi"
              cpu: "1000m"  # 1000m is equal to 1 VCPU core                                          
```

## Step-03: Create k8s objects & Test
```
# Create All Objects
kubectl apply -f kube-manifests/

# List Pods
kubectl get pods

# Watch List Pods screen
kubectl get pods -w

# Describe Pod & Discuss about init container
kubectl describe pod <usermgmt-microservice-xxxxxx>

# Access Application Health Status Page
http://<WorkerNode-Public-IP>:31231/usermgmt/health-status

# List Nodes & Describe Node
kubectl get nodes
kubectl describe node <Node-Name>
```
## Step-04: Clean-Up
- Delete all k8s objects created as part of this section
```
# Delete All
kubectl delete -f kube-manifests/

# List Pods
kubectl get pods

# Verify sc, pvc, pv
kubectl get sc,pvc,pv
```

## References:
- https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/

------------------------------
# Namespaces
----------------------------
1. Namespaces - Imperative using kubectl
2. Namespaces -  Declarative using YAML & LimitRange
3. Namespaces -  Declarative using YAML & ResourceQuota


# Kubernetes Namespaces - Imperative using kubectl
------------------------------------------------------

## Step-01: Introduction
- Namespaces allow to split-up resources into different groups.
- Resource names should be unique in a namespace
- We can use namespaces to create multiple environments like dev, staging and production etc
- Kubernetes will always list the resources from `default namespace` unless we provide exclusively from which namespace we need information from.

## Step-02: Namespaces Generic - Deploy in Dev1 and Dev2
### Create Namespace
```
# List Namespaces
kubectl get ns 

# Craete Namespace
kubectl create namespace <namespace-name>
kubectl create namespace dev1
kubectl create namespace dev2

# List Namespaces
kubectl get ns 
```
### Comment NodePort in UserMgmt NodePort Service
- **File: 07-UserManagement-Service.yml**
- **Why?:**
  - Whenever we create with same manifests multiple environments like dev1, dev2 with namespaces, we cannot have same worker node port for multiple services. 
  - We will have port conflict. 
  - Its good for k8s system to provide dynamic nodeport for us in such situations.
```yml
      #nodePort: 31231
```
- **Error** if not commented
```log
The Service "usermgmt-restapp-service" is invalid: spec.ports[0].nodePort: Invalid value: 31231: provided port is already allocated
```
### Deploy All k8s Objects
```
# Deploy All k8s Objects
kubectl apply -f kube-manifests/ -n dev1
kubectl apply -f kube-manifests/ -n dev2

# List all objects from dev1 & dev2 Namespaces
kubectl get all -n dev1
kubectl get all -n dev2
```
## Step-03: Verify SC,PVC and PV
- **Shorter Note**
  - PVC is a namespace specific resource
  - PV and SC are generic
- **Observation-1:** `Persistent Volume Claim (PVC)` gets created in respective namespaces
```
# List PVC for dev1 and dev2
kubectl get pvc -n dev1
kubectl get pvc -n dev2
```
- **Observation-2:** `Storage Class (SC) and Persistent Volume (PV)` gets created generic. No specifc namespace for them   
```
# List sc,pv
kubect get sc,pv
```
## Step-04: Access Application
### Dev1 Namespace
```
# Get Public IP
kubectl get nodes -o wide

# Get NodePort for dev1 usermgmt service
kubectl get svc -n dev1

# Access Application
http://<Worker-Node-Public-Ip>:<Dev1-NodePort>/usermgmt/health-stauts
```
### Dev2 Namespace
```
# Get Public IP
kubectl get nodes -o wide

# Get NodePort for dev2 usermgmt service
kubectl get svc -n dev2

# Access Application
http://<Worker-Node-Public-Ip>:<Dev2-NodePort>/usermgmt/health-stauts
```
## Step-05: Clean-Up
```
# Delete namespaces dev1 & dev2
kubectl delete ns dev1
kubectl delete ns dev2

# List all objects from dev1 & dev2 Namespaces
kubectl get all -n dev1
kubectl get all -n dev2

# List Namespaces
kubectl get ns

# List sc,pv
kubectl get sc,pv

# Delete Storage Class
kubectl delete sc ebs-sc

# Get all from All Namespaces
kubectl get all -all-namespaces
```

## References:
- https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/


# Kubernetes Namespaces - LimitRange - Declarative using YAML
-------------------------------------------------------------
## Step-01: Create Namespace manifest
- **Important Note:** File name starts with `00-`  so that when creating k8s objects namespace will get created first so it don't throw an error.
```yml
apiVersion: v1
kind: Namespace
metadata:
  name: dev3
```

## Step-02: Create LimitRange manifest
- Instead of specifying `resources like cpu and memory` in every container spec of a pod defintion, we can provide the default CPU & Memory for all containers in a namespace using `LimitRange`
```yml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: ns-resource-quota
  namespace: dev3
spec:
  limits:
    - default:
        memory: "512Mi" # If not specified the Container's memory limit is set to 512Mi, which is the default memory limit for the namespace.
        cpu: "500m"  # If not specified default limit is 1 vCPU per container 
      defaultRequest:
        memory: "256Mi" # If not specified default it will take from whatever specified in limits.default.memory
        cpu: "300m" # If not specified default it will take from whatever specified in limits.default.cpu
      type: Container                        
```

## Step-03: Update all k8s manifest with namespace
- Update all files from 02 to 08 with `namespace: dev3` in top metadata section in folder `kube-manifests/02-Declarative` 
- **Example**
```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-mysql-pv-claim
  namespace: dev3
```

## Step-04: Create k8s objects & Test
```
# Create All Objects
kubectl apply -f kube-manifests/

# List Pods
kubectl get pods -n dev3 -w

# View Pod Specification (CPU & Memory)
kubectl get pod <pod-name> -o yaml -n dev3

# Get & Describe Limits
kubectl get limits -n dev3
kubectl describe limits default-cpu-mem-limit-range -n dev3

# Get NodePort
kubectl get svc -n dev3

# Get Public IP of a Worker Node
kubectl get nodes -o wide

# Access Application Health Status Page
http://<WorkerNode-Public-IP>:<NodePort>/usermgmt/health-status

```
## Step-05: Clean-Up
- Delete all k8s objects created as part of this section
```
# Delete All
kubectl delete -f kube-manifests/
```







## References:
- https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/
- https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-default-namespace/
- https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/


# Kubernetes Namespaces - ResourceQuota - Declarative using YAML
--------------------------------------------------------------------

## Step-01: Create Namespace manifest
- **Important Note:** File name starts with `00-`  so that when creating k8s objects namespace will get created first so it don't throw an error.
```yml
apiVersion: v1
kind: Namespace
metadata:
  name: dev3
```

## Step-02: Create ResourceQuota manifest
```yml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: ns-resource-quota
  namespace: dev3
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi  
    pods: "5"    
    configmaps: "5" 
    persistentvolumeclaims: "5" 
    replicationcontrollers: "5" 
    secrets: "5" 
    services: "5"                      
```


## Step-03: Create k8s objects & Test
```
# Create All Objects
kubectl apply -f kube-manifests/

# List Pods
kubectl get pods -n dev3 -w

# View Pod Specification (CPU & Memory)
kubectl get pod <pod-name> -o yaml -n dev3

# Get & Describe Limits
kubectl get limits -n dev3
kubectl describe limits default-cpu-mem-limit-range -n dev3

# Get Resource Quota 
kubectl get quota -n dev3
kubectl describe quota ns-resource-quota -n dev3

# Get NodePort
kubectl get svc -n dev3

# Get Public IP of a Worker Node
kubectl get nodes -o wide

# Access Application Health Status Page
http://<WorkerNode-Public-IP>:<NodePort>/usermgmt/health-status

```
## Step-04: Clean-Up
- Delete all k8s objects created as part of this section
```
# Delete All
kubectl delete -f kube-manifests/
```

## References:
- https://kubernetes.io/docs/tasks/administer-cluster/namespaces-walkthrough/
- https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/quota-memory-cpu-namespace/


## Additional References:
- https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/cpu-constraint-namespace/ 
- https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-constraint-namespace/