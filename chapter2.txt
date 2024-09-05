# Chapter 2: Kubernetes Architecture (Expanded)

## 2.1 Overview of Kubernetes Architecture

Kubernetes follows a master-worker architecture, consisting of two main types of nodes:
1. Control Plane (Master) Nodes
2. Worker Nodes

Together, these nodes form a Kubernetes cluster. Let's dive into each component in detail.

## 2.2 Control Plane Components

The Control Plane is responsible for managing the cluster state and making global decisions. It consists of several components:

### 2.2.1 kube-apiserver
- Primary entry point for all cluster operations
- Exposes the Kubernetes API
- Handles REST operations and validates them
- Acts as a front-end to the cluster's shared state

Key features:
- Horizontal scaling for high availability
- Authenticates and authorizes API requests
- Serves as the cluster's gateway

### 2.2.2 etcd
- Distributed key-value store
- Stores all cluster data (configuration, state, metadata)
- Provides reliable, strongly consistent storage

Important characteristics:
- Highly available (typically deployed as a cluster)
- Uses the Raft consensus algorithm
- Supports watch operations for change notifications

### 2.2.3 kube-scheduler
- Assigns newly created pods to nodes
- Takes into account factors such as:
  - Resource requirements
  - Hardware/software constraints
  - Affinity and anti-affinity specifications
  - Data locality
  - Deadlines

Scheduling process:
1. Filtering: Finds feasible nodes for the pod
2. Scoring: Ranks the feasible nodes to choose the best one

### 2.2.4 kube-controller-manager
- Runs controller processes
- Monitors the cluster state through the API server
- Works to move the current state towards the desired state

Key controllers:
- Node Controller: Manages node lifecycle
- Replication Controller: Maintains the correct number of pods
- Endpoints Controller: Populates the Endpoints object
- Service Account & Token Controllers: Create default accounts and API access tokens

### 2.2.5 cloud-controller-manager (optional)
- Integrates with cloud-specific control logic
- Allows cloud providers to integrate with Kubernetes
- Manages cloud-specific components like load balancers and storage volumes

## 2.3 Worker Node Components

Worker nodes are the machines that run containerized applications. They include:

### 2.3.1 kubelet
- Primary node agent that runs on each node
- Ensures containers are running in a pod
- Works with PodSpecs (YAML or JSON object describing a pod)
- Reports node and pod status to the control plane

Key responsibilities:
- Pod lifecycle management
- Volume mounting
- Container health checks
- Resource monitoring and reporting

### 2.3.2 kube-proxy
- Network proxy that runs on each node
- Implements part of the Kubernetes Service concept
- Maintains network rules on nodes
- Performs connection forwarding or load balancing for Service cluster IPs

Modes of operation:
- IPTABLES mode (default)
- IPVS mode (for high-performance requirements)
- Userspace mode (legacy)

### 2.3.3 Container Runtime
- Software responsible for running containers
- Kubernetes supports several container runtimes:
  - containerd
  - CRI-O
  - Docker Engine (via cri-dockerd)
  - Any implementation of the Kubernetes CRI (Container Runtime Interface)

## 2.4 Add-ons and Supporting Components

These are additional components that extend Kubernetes functionality:

### 2.4.1 DNS
- Cluster DNS server (typically CoreDNS)
- Provides DNS records for Kubernetes services and pods

### 2.4.2 Web UI (Dashboard)
- General-purpose web-based UI for cluster management

### 2.4.3 Container Resource Monitoring
- Records generic time-series metrics about containers
- Provides a UI for browsing that data

### 2.4.4 Cluster-level Logging
- Mechanism to save container logs to a central log store

## 2.5 Kubernetes Objects and Workload Resources

Kubernetes uses various objects to represent the state of your cluster:

### 2.5.1 Basic Objects
- Pod: Smallest deployable unit, contains one or more containers
- Service: Defines a logical set of Pods and a policy to access them
- Volume: Directory accessible to containers in a pod
- Namespace: Virtual cluster, used for multi-tenancy

### 2.5.2 Controllers
- ReplicaSet: Ensures a specified number of pod replicas are running
- Deployment: Provides declarative updates for Pods and ReplicaSets
- StatefulSet: Manages stateful applications
- DaemonSet: Ensures all (or some) nodes run a copy of a Pod
- Job: Runs a task to completion
- CronJob: Creates Jobs on a time-based schedule

Understanding this architecture is crucial for effectively deploying, managing, and troubleshooting Kubernetes clusters.
