# Chapter 4: Kubernetes Objects and Workloads

## 4.1 Introduction to Kubernetes Objects

Kubernetes objects are persistent entities in the Kubernetes system. They represent the state of your cluster, including what containerized applications are running, the resources available to those applications, and the policies around how those applications behave.

## 4.2 Basic Kubernetes Objects

### 4.2.1 Pods
- Smallest deployable units of computing in Kubernetes
- Can contain one or more containers
- Share storage and network resources
- Example:
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: nginx
  spec:
    containers:
    - name: nginx
      image: nginx:1.14.2
      ports:
      - containerPort: 80
  ```

### 4.2.2 Services
- An abstract way to expose an application running on a set of Pods
- Types: ClusterIP, NodePort, LoadBalancer, ExternalName
- Example:
  ```yaml
  apiVersion: v1
  kind: Service
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

### 4.2.3 Volumes
- Directory accessible to containers in a pod
- Types: emptyDir, hostPath, configMap, secret, persistentVolumeClaim, etc.

### 4.2.4 Namespaces
- Virtual clusters backed by the same physical cluster
- Used for multi-tenancy and resource isolation

## 4.3 Controllers

### 4.3.1 ReplicaSet
- Ensures that a specified number of pod replicas are running at any given time
- Usually used indirectly via Deployments

### 4.3.2 Deployment
- Provides declarative updates for Pods and ReplicaSets
- Supports rolling updates and rollbacks
- Example:
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nginx-deployment
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
          image: nginx:1.14.2
          ports:
          - containerPort: 80
  ```

### 4.3.3 StatefulSet
- Manages the deployment and scaling of a set of Pods
- Provides guarantees about the ordering and uniqueness of these Pods
- Used for stateful applications

### 4.3.4 DaemonSet
- Ensures that all (or some) Nodes run a copy of a Pod
- Used for cluster-wide services like log collection or monitoring

### 4.3.5 Job
- Creates one or more Pods and ensures that a specified number of them successfully terminate
- Used for batch processes

### 4.3.6 CronJob
- Creates Jobs on a time-based schedule

## 4.4 Config and Storage Objects

### 4.4.1 ConfigMap
- Stores non-confidential data in key-value pairs
- Can be used as environment variables, command-line arguments, or config files in a volume

### 4.4.2 Secret
- Similar to ConfigMap but for confidential data
- Stored in encrypted form

### 4.4.3 PersistentVolume (PV) and PersistentVolumeClaim (PVC)
- PV: A piece of storage in the cluster provisioned by an administrator
- PVC: A request for storage by a user

## 4.5 Metadata Objects

### 4.5.1 HorizontalPodAutoscaler
- Automatically scales the number of pods in a replication controller, deployment, replica set or stateful set

### 4.5.2 LimitRange
- Provides constraints to limit resource consumption per containers or pods in a namespace

### 4.5.3 ResourceQuota
- Provides constraints that limit aggregate resource consumption per namespace

## 4.6 Working with Kubernetes Objects

- Objects are typically defined in YAML files
- Use `kubectl apply -f <filename>` to create or update objects
- Use `kubectl get <object-type>` to list objects
- Use `kubectl describe <object-type> <object-name>` for detailed information
- Use `kubectl delete <object-type> <object-name>` to delete objects

Understanding these objects and how they interact is crucial for effectively deploying and managing applications in Kubernetes.
