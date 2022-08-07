---
layout: post
author: Lucas
title: Kubernetes Basics
subtitle: Note Repository
categories: [kubernetes]
tags: [devops, kubernetes, containers]
---
<!--🔴 🟠 ⚫ ⚪ 🟣 🟢 🟡 🔵-->

## References

[The Kubernetes Handbook](https://www.freecodecamp.org/news/the-kubernetes-handbook/#introduction-to-container-orchestration-and-kubernetes)

[GitHub - fhsinchy/kubernetes-handbook-projects](https://github.com/fhsinchy/kubernetes-handbook-projects)

## Useful Code Snippets

```bash
# Create 'pod/<pod_name>'
# Run <image_name> inside pod
kubectl run <pod_name> \
--image=<image_name> \
--port=80
```

```bash
# 'ls -la' for k8s
kubectl get pod/service
```

```bash
# Create 'service/<pod_name>'
# Run load balancer as a service
kubectl expose pod <pod_name> \
--type=LoadBalancer \
--port=80
```

```bash
# Delete resource based on type and name
kubectl delete <type> <name>
```

## Basics

🚢 Container orchestration is the process of automating the `deployment`, `management`, `scaling`, and `networking` tasks of containers.

### Architecture

![Kubernetes Architecture](/assets/images/2021-12-18-kubernetes-basics/k8s_architecture.png)

- `kubectl`: K8s CLI tool to communicate with your cluster via API
- `node`: the machine, virtual or physical, that hosts the pods. Usually refers to worker nodes that run the containerized workloads, but could also refer to control plane nodes (centralized API, cluster management).
  - `kubelet`: primary agent in a node, does things such as start pods & containers or register the node with the apiserver. Receives instructions from the control plane, keeps etcd updated
  - `kube-proxy`: runs on each node to maintain network rules. Any network request that reaches a service inside the cluster passes through the kube-proxy service
- `control plane`: cluster management
  - `etcd`: data store that maintains a record of the state of the cluster in a distributed key-value store
  - `kube-scheduler`: queries state of the cluster (at etcd) to schedule/assign workloads to worker nodes, making sure no server is overloaded
  - `kube-api-server`: gateway to query and interact with cluster. Validates and processes requests delivered using client libraries like kubectl
  - `cloud-controller-manager`: interacts with cloud provider's (GKE, EKS) API. Keeps local and cloud interactions isolated
  - `kube-controller-manager`: responsible for controlling the state of the cluster. Implements and tracks the lifecycle of the controllers deployed to the cluster. Main workloads managed by kube-controller-manager:
    - `Deployment`: declarative and good for managing stateless apps (pods are interchangeable and replaceable), it matches the current state of your cluster to the desired state mentioned in the Deployment manifest (YAML). e.g. If you create a deployment with 1 replica, it will check that the desired state of ReplicaSet is 1 and current state is 0, so it will create a ReplicaSet, which will further create the pod.
    - `StatefulSet`: lets you run 1+ pods that track state in some manner. Helps match pods with persistent volumes.
    - `DaemonSet`: ensures that the pod specified via YAML runs on all the nodes of the cluster. If a node is added/removed from a cluster, DaemonSet automatically adds/deletes the pod. e.g. Installing a monitoring agent on each node.

### Objects

- `pods`: self-contained, easily replicable objects. Can host a container or a group of (very closely related — more than back-end app + db) containers with shared network IP address and filesystem volumes, meant to run a single instance of an application.
- `services`: since pods are ephemeral (destroyed and replaced, not recycled — new IP every time), a service is an abstraction that groups a logical set of pods and presents them as a single entity, e.g. LoadBalancer, ClusterIP, NodePort, ExternalName
  - `LoadBalancer`: exposes a group of pods as one entity to the outside (of the cluster). The pod(s) become(s) exposed on the port specified. Can be accessed via `minikube service <service_name>`, which accesses the cluster's IP + the ephemeral exposed port, which is mapped to the port exposed by the load balancer.
  - `ClusterIP`: same as LoadBalancer but only within the cluster
- `Controllers`: non-terminating control loops that watch the state of the cluster and make/request changes accordingly. There are controllers to manage each type of workload supported by Kubernetes.

## Declarative App Example

### Directory Tree

- src /
  - (app code)
- k8s /
  - example-pod.yaml

    ```yaml
    apiVersion: v1 # k8s API version
    kind: Pod # object type
    metadata: # useful for future referencing
        name: example-pod
        labels: # services select objects via labels
        component: web
    spec: # specify state wanted for object
        containers:
        - name: example
        image: example/latest
        ports:
        - containerPort: 80
      ```

  - example-lb-service.yaml

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
        name: example-lb-service
    spec:
        type: LoadBalancer # other options: ClusterIP, NodePort, ExternalName
        ports:
        - port: 80 # port for others to access LB pod
        targetPort: 80 # containers' port (must match)
        selector: # ID objects connected to the service
        component: web
    ```

- Dockerfile

  ```bash
  # copies the whole directory into node image directory
  # installs package.json with npm, then runs build
  # copies compiled app into nginx image with port 80 exposed
  # after that, image can run as container with port 80 publicly accessible
  ```

- index.html
- package.json

### Execution

  ```bash
  # Both 'apply' commands can be consolidated with 'kubectl apply -f <directory>'

  kubectl apply -f example-pod.yaml
  
  # pod/example-pod created
  
  kubectl get pod
  
  # NAME         READY   STATUS    RESTARTS   AGE
  # example-pod   1/1     Running   0          3m3s
  
  kubectl apply -f example-lb-service.yaml
  
  # service/example-lb-service created
  
  kubectl get service
  
  # NAME                 TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
  # example-lb-service   LoadBalancer   10.107.231.120   <pending>     80:30848/TCP   7s
  ```

  ```bash
  # Example if running locally with minikube

  minikube service example-lb-service
  
  # |-----------|--------------------|-------------|-----------------------------|
  # | NAMESPACE |        NAME        | TARGET PORT |             URL             |
  # |-----------|--------------------|-------------|-----------------------------|
  # | default   | example-lb-service |          80 | http://192.168.99.101:30848 |
  # |-----------|--------------------|-------------|-----------------------------|
  # ?  Opening service default/example-lb-service in default browser...
  ```

## Multi-Container App Example

### Architecture

![Untitled](/assets/images/2021-12-18-kubernetes-basics/multi-container-app-arch.png)

- Note that the ClusterIP works like a LoadBalancer and can point to a single pod or a set of pods, but it can only serve requests from within the cluster

### Directory Tree
  
- app /
  - (app source code, including Dockerfile)
- postgres /
  - (db Dockerfiles and init file)
- k8s /
  - app-deployment.yaml

    ```yaml
    apiVersion: apps/v1 # based on docs
    kind: Deployment
    metadata:
        name: app-deployment
    spec: # controller specs
        replicas: 3
        selector: 
        matchLabels:
        component: app # all pods with this label will be controlled
        template: # container specs and metadata
        metadata:
        labels:
        component: app # all pods created will have this label
        spec:
        containers:
        - name: app
            image: app/latest
            ports:
            - containerPort: 3000
            env:
            - name: DB_CONNECTION
            value: pg
            - name: DB_HOST # use service name to reach db host
                    value: postgres-cluster-ip-service
                - name: DB_PORT
                    value: '5432'
                - name: DB_USER
                    value: postgres
                - name: DB_DATABASE # db to be used inside host
                    value: notesdb
                - name: DB_PASSWORD
                    value: 63eaQB9wtLqmNBpg
    ```

  - postgres-deployment.yaml

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
        name: postgres-deployment
    spec:
        replicas: 1
        selector:
        matchLabels:
            component: postgres
        template:
        metadata:
            labels:
            component: postgres
        spec:
        volumes: # pods' storage assignments and claims
            - name: postgres-storage
                persistentVolumeClaim:
                claimName: database-persistent-volume-claim
            containers:
            - name: postgres
                image: postgres/latest
                ports:
                - containerPort: 5432
            volumeMounts: # connect pod volumes to container
                - name: postgres-storage
                    mountPath: /var/lib/postgresql/data # container dir
                    subPath: postgres # dir created inside vol for container
                env:
                - name: POSTGRES_PASSWORD # used to connect to db via API
                    value: 63eaQB9wtLqmNBpg
                - name: POSTGRES_DB
                    value: appdb
    ```

  - database-persistent-volume.yaml

    ```yaml
    # Not actually used, just an example

    apiVersion: v1
    kind: PersistentVolume
    metadata:
        name: database-persistent-volume
    spec:
        storageClassName: manual
        capacity:
        storage: 5Gi
        accessModes:
        - ReadWriteOnce # mounted as r/w by one single node
        # - ReadOnlyMany # many nodes can mount it as read only
        hostPath: # development-specific, look into carefully on creation
        path: "/mnt/data"
    ```

  - database-persistent-volume-claim.yaml

    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
        name: database-persistent-volume-claim
    spec:
        #storageClassName: manual # removing this dynamically creates volume
        accessModes:
        - ReadWriteOnce
        resources:
        requests:
            storage: 2Gi
      ```

  - postgres-cluster-ip-service.yaml

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
        name: postgres-cluster-ip-service
    spec:
        type: ClusterIP
        selector:
        component: postgres
        ports:
        - port: 5432
            targetPort: 5432
    ```

  - app-lb-service.yaml

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
        name: app-lb-service
    spec:
        type: LoadBalancer
        ports:
        - port: 3000
            targetPort: 3000
        selector:
        component: app
    ```

- docker-compose.yaml

  ```yaml
  version: "3.8"
  
  services: 
      db:
          build:
              # custom image w/ app table pre-created
              context: ./postgres
              dockerfile: Dockerfile.dev
          volumes: 
              - db-data:/var/lib/postgresql/data
          environment:
              POSTGRES_PASSWORD: 63eaQB9wtLqmNBpg
              POSTGRES_DB: notesdb
      app:
          build: 
              context: ./app
              dockerfile: Dockerfile.dev
          ports: 
              - 3000:3000
          volumes: 
              - /home/node/app/node_modules
              - ./app:/home/node/app
          environment: 
              DB_CONNECTION: pg
              DB_HOST: db
              DB_PORT: 5432
              DB_USER: postgres
              DB_DATABASE: notesdb
              DB_PASSWORD: 63eaQB9wtLqmNBpg
  
  volumes:
      db-data:
          name: app-db-dev-data
  ```

### Execution

- Debug session example:

  ```bash
  kubectl apply -f app-deployment.yaml
  
  # deployment.apps/app-deployment created
  
  kubectl get deployment
  
  # NAME             READY   UP-TO-DATE   AVAILABLE   AGE
  # app-deployment   0/3     3            0           18m2s
  
  kubectl get -f app-deployment.yaml
  
  # NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES               SELECTOR
  # app-deployment   0/3     3            0           19m   app          app/latest           component=app
  
  kubectl get pod
  
  # NAME                             READY   STATUS             RESTARTS   AGE
  # app-deployment-d59f9c884-88j45   0/1     CrashLoopBackOff   10         30m
  # app-deployment-d59f9c884-96hfr   0/1     CrashLoopBackOff   10         30m
  # app-deployment-d59f9c884-pzdxg   0/1     CrashLoopBackOff   10         30m
  
  kubectl describe pod api-deployment-d59f9c884-88j45
  
  # (Extensive debug info)
  
  kubectl logs api-deployment-d59f9c884-88j45
  
  # (Logs for container -- error with image)
  ```

- After fixing image:

  ```bash
  kubectl apply -f api-deployment.yaml
  
  # deployment.apps/api-deployment configured
  
  kubectl get deployment
  
  # NAME             READY   UP-TO-DATE   AVAILABLE   AGE
  # app-deployment   3/3     3            3           6m
  
  kubectl get pod
  
  # NAME                              READY   STATUS    RESTARTS   AGE
  # app-deployment-66cdd98546-l9x8q   1/1     Running   0          7m26s
  # app-deployment-66cdd98546-mbfw9   1/1     Running   0          7m31s
  # app-deployment-66cdd98546-pntxv   1/1     Running   0          7m21s
  
  kubectl apply -f postgres-deployment.yaml
  
  # deployment.apps/postgres-deployment created
  
  kubectl get deployment
  
  # NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
  # postgres-deployment   1/1     1            1           13m
  
  kubectl get pod
  
  # NAME                                   READY   STATUS    RESTARTS   AGE
  # postgres-deployment-76fcc75998-mwnb7   1/1     Running   0          13m
  
  kubectl apply -f database-persistent-volume-claim.yaml
  
  # persistentvolumeclaim/database-persistent-volume-claim created
  
  kubectl get persistentvolumeclaim
  
  # NAME                               STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
  # database-persistent-volume-claim   Bound    pvc-525ae8af-00d3-4cc7-ae47-866aa13dffd5   2Gi        RWO            standard       2s
  
  kubectl apply -f postgres-cluster-ip-service.yaml
  kubectl apply -f app-lb-service.yaml
  ```

- Short version:

  ```yaml
  kubectl apply -f k8s
  
  # deployment.apps/app-deployment created
  # service/app-load-balancer-service created
  # persistentvolumeclaim/database-persistent-volume-claim created
  # service/postgres-cluster-ip-service created
  # deployment.apps/postgres-deployment created
  
  kubectl get deployment
  
  # NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
  # app-deployment        3/3     3            3           106s
  # postgres-deployment   1/1     1            1           106s
  ```

## Further Education

- [Kubernetes Documentation](https://kubernetes.io/docs/home/): go through "Getting Started", "Tutorials", "Concepts".
- [Task Catalog](https://kubernetes.io/docs/tasks/): How to do common Kubernetes tasks.
- [Reference page](https://kubernetes.io/docs/reference/) - all things API, setup (kubeadm) and component (kubelet, kube-proxy, TLS bootstrapping, etc) tools, kubectl, etc.
