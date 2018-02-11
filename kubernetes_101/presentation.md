name: inverse
layout: true
class: left, middle, inverse
---
# Kubernetes 101
---
# Containers
.center[![docker_container](img/docker-vm-container.png)]
---
# Microservices and orchestration
.center[![failed_containers](img/failed-containers.jpg)]
---
# What is Kubernetes

Kubernetes is an open-source system for automating deployment, scaling and management of containerized applications.
---
# Architecture
![kube-arch](img/kube-arch.png)
---
# Web-UI
![kube-ui](img/ui.png)
---
# kubectl
![kubectl](img/kubectl.png)
---
# Objects
Kubernetes Objects are persistent entities in the Kubernetes system. Kubernetes uses these entities to represent the state of your cluster. 
Most often, you provide the information to kubectl in a .yaml file
---
# Core primitives
## Pod
- the atom of replication and placement
- small group of containers and volumes
- each pod gets an IP address
---
# Core primitives
## Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
```
---
# Core primitives
## Volumes
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      path: /data
      type: Directory
```
---
# Core primitives
## Volume types
- awsElasticBlockStore
- azureDisk
- azureFile
- cephfs
- configMap
- emptyDir
- fc (fibre channel)
- gcePersistentDisk
- gitRepo
- glusterfs
- hostPath
- iscsi
- nfs
- persistentVolumeClaim
- secret
- storageos
- vsphereVolume
- ...
---
# Core primitives
## Secrets
A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key. Each item must be base64 encoded.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```
---
# Core primitives
## Using secrets
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
```
---
# Core primitives
## Using secrets
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: mysecret
            key: password
  restartPolicy: Never
```
---
# Core primitives
## Config maps
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  special.level: very
  special.type: charm
```
---
# Core primitives
## Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```
---
# Core primitives
## Service
Service is an abstraction which defines a logical set of Pods and a policy by which to access them - sometimes called a micro-service.
```yaml
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
```
---
# Core primitives
## Service
![kube-services](img/services.svg)
---
# Core primitives
## Service types.
- __ClusterIP__: Exposes the service on a cluster-internal IP. Choosing this value makes the service only reachable from within the cluster. This is the default ServiceType.
- __NodePort__: Exposes the service on each Node’s IP at a static port (the NodePort). A ClusterIP service, to which the NodePort service will route, is automatically created. You’ll be able to contact the NodePort service, from outside the cluster, by requesting <NodeIP>:<NodePort>.
- __LoadBalancer__: Exposes the service externally using a cloud provider’s load balancer. NodePort and ClusterIP services, to which the external load balancer will route, are automatically created.
- __ExternalName__: Maps the service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record with its value. No proxying of any kind is set up. This requires version 1.7 or higher of kube-dns
---
# Core primitives
## Selectors
- user-provided key-value attributes
- attached to any API object
- queryable by selectors
- the only groupping mechanism
---
# Core primitives
## Other
- DeamonSet
- Jobs
- Namespaces (default, kube-system...)
- DNS
---
# Hometask
1. Try Minikube
 - install kubectl
 - install minikube
 - run kubernetes cluster with minikube
 - try web-ui `minikube dashboard`
 - try kubectl
2. Run your first application inside Minikube
 - follow [the tutorial](https://kubernetes.io/docs/tasks/run-application/run-stateless-application-deployment/)
3. (Optional) Run Wordpress inside Minikube
 - follow [the tutorial](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/)
---
# Reading list
## Jonathan Baier, Getting Started with Kubernetes, Second edition, 2017
.center[![Getting Started with Kubernetes](img/book.png)]