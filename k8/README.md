# Kubernetes Notes

Built from my own notes (`kubernetes.txt`, `web setup/README.md`) and the working MongoDB + Mongo Express setup in `web setup/`.

## Index

1. [What is Kubernetes](#1-what-is-kubernetes)
2. [Core Components](#2-core-components)
   - 2.1 [Components table](#21-components-table)
   - 2.2 [Services](#22-services)
   - 2.3 [Deployment, ReplicaSet and Pod](#23-deployment-replicaset-and-pod)
3. [Cluster Architecture](#3-cluster-architecture)
   - 3.1 [Worker node](#31-worker-node)
   - 3.2 [Master node](#32-master-node)
   - 3.3 [What happens when you deploy an app](#33-what-happens-when-you-deploy-an-app)
4. [Local Setup: Minikube and kubectl](#4-local-setup-minikube-and-kubectl)
5. [kubectl Commands](#5-kubectl-commands)
6. [Configuration Files (YAML)](#6-configuration-files-yaml)
   - 6.1 [Structure](#61-structure)
   - 6.2 [Labels and selectors](#62-labels-and-selectors)
   - 6.3 [Ports](#63-ports)
7. [Web App Setup: MongoDB + Mongo Express](#7-web-app-setup-mongodb--mongo-express)
   - 7.1 [Plan and folder layout](#71-plan-and-folder-layout)
   - 7.2 [Secret](#72-secret)
   - 7.3 [ConfigMap](#73-configmap)
   - 7.4 [MongoDB deployment and internal service](#74-mongodb-deployment-and-internal-service)
   - 7.5 [Mongo Express deployment and service](#75-mongo-express-deployment-and-service)
   - 7.6 [Apply everything and test](#76-apply-everything-and-test)
   - 7.7 [Change and remove](#77-change-and-remove)
8. [Namespaces](#8-namespaces)
9. [Ingress](#9-ingress)
   - 9.1 [What it does](#91-what-it-does)
   - 9.2 [Ingress controller](#92-ingress-controller)
   - 9.3 [Setup in Minikube](#93-setup-in-minikube)
   - 9.4 [Ingress rules for the web app](#94-ingress-rules-for-the-web-app)
   - 9.5 [Multiple paths and hosts](#95-multiple-paths-and-hosts)
   - 9.6 [Default backend](#96-default-backend)
   - 9.7 [TLS certificate](#97-tls-certificate)
10. [Helm](#10-helm)

---

## 1. What is Kubernetes

Kubernetes (k8s) is an open-source container orchestration platform. It runs, scales and repairs containerized applications across a group of machines.

What it offers:
1. **High availability**: the app stays up when parts of it fail.
2. **Scalability**: more or fewer copies of the app, depending on load.
3. **Disaster recovery**: backup and restore.

You describe the **desired state** (for example "run 3 copies of this app") and Kubernetes keeps the cluster in that state. If a container crashes, it starts a new one.

---

## 2. Core Components

### 2.1 Components table

| Component | What it is |
|---|---|
| **Node** | A server or machine in the cluster. It is either a master node or a worker node. |
| **Pod** | The smallest unit in Kubernetes. A wrapper around one or more containers, with a shared IP, network and storage. Each pod gets its own IP. If a pod crashes, a new pod starts and gets a **new** IP. |
| **Service** | A permanent IP address in front of pods. If a pod dies, the Service and its IP stay. It also load-balances across the pods of the same app. |
| **Ingress** | Gives an app a readable URL instead of an IP. The request goes to the Ingress first, then to a Service. |
| **ConfigMap** | Stores non-sensitive configuration, such as a database URL. When the config changes you do not need to rebuild the image. Do not put usernames or passwords here. |
| **Secret** | Like a ConfigMap, but for sensitive data such as usernames and passwords. Values are base64 encoded. |
| **Volume** | Persistent storage. It lives outside the pod, and its data survives pod restarts. |
| **Deployment** | A blueprint for pods. It declares how many replicas to run, and you can scale up and down. |
| **StatefulSet** | Like a Deployment, but for databases. Replicating a database with a plain Deployment causes data sync problems. A StatefulSet keeps reads and writes consistent. |

### 2.2 Services

- **Internal service**: only components inside the cluster can reach it. Used for things like a database. Type `ClusterIP`, which is the default.
- **External service**: makes the app reachable from a browser. Type `LoadBalancer` (or `NodePort`).
- A Service is also a load balancer. If two pods run the same app, it forwards each request to one of them.

```text
Browser --> Service (stable IP) --> Pod 1 (IP changes if it restarts)
                               \--> Pod 2
```

### 2.3 Deployment, ReplicaSet and Pod

We do not create pods directly. We create a **Deployment**, and Kubernetes creates a **ReplicaSet** between the Deployment and the pods automatically.

```text
Deployment   manages   ReplicaSet
ReplicaSet   manages   all replicas of a Pod
Pod          wraps     the container(s)
```

---

## 3. Cluster Architecture

A production cluster has multiple master nodes and multiple worker nodes.

```text
+------------------- Master node (control plane) -------------------+
|  API Server     Scheduler     Controller Manager     etcd          |
+--------------------------------------------------------------------+
           |                         |                       |
+------ Worker node 1 ------+  +------ Worker node 2 ------+
| kubelet  kube-proxy       |  | kubelet  kube-proxy       |
| container runtime         |  | container runtime         |
|  [Pod] [Pod] [Pod]        |  |  [Pod] [Pod]              |
+---------------------------+  +---------------------------+
```

### 3.1 Worker node

Worker nodes do the actual work. Each node runs many pods and has 3 processes installed:

1. **Container runtime** (for example Docker): runs the containers.
2. **Kubelet**: starts and manages the pods and containers on its node. It talks to both the container runtime and the pods.
3. **Kube-proxy**: forwards requests from Services to the pods.

### 3.2 Master node

The master node controls the cluster state and the worker nodes. It runs 4 processes. In practice there can be several master nodes. Master processes need fewer resources than worker processes.

1. **API Server**: the cluster gateway. Clients (kubectl, UI, dashboard) talk to it. It authenticates, validates the request (new service, new app, new pod) and passes it on.
2. **Scheduler**: after the API server validates a request, the scheduler decides which worker node runs the new pod. It looks for the least busy node.
3. **Controller manager**: detects state changes, such as a pod crashing, and tries to restore the desired state by asking the scheduler to schedule a replacement.
4. **etcd**: a key-value store and the cluster brain. Every change in the cluster is saved here, so the scheduler and controller manager know the current state.

### 3.3 What happens when you deploy an app

```text
kubectl apply
   |
   v
API Server  (authenticate, validate, save to etcd)
   |
   v
Scheduler   (pick the least busy worker node)
   |
   v
Kubelet on that node  (start the pod through the container runtime)

Controller manager watches: if a pod dies, ask the scheduler for a new one
```

---

## 4. Local Setup: Minikube and Kubectl

You cannot run a full multi-node cluster locally, so use Minikube.

- **Minikube**: a one-node cluster that runs both master and worker processes on the same node, with a container runtime already installed. It runs in a VM or a Docker container, depending on the driver.
- **kubectl**: the command-line tool for the cluster. To talk to the API server you need a client, and kubectl is that client.

```bash
minikube version
kubectl version --client

minikube start
minikube status
kubectl get nodes
```

---

## 5. kubectl Commands

Inspect resources:

```bash
kubectl get nodes
kubectl get pod
kubectl get pod -o wide               # more output, such as pod IPs
kubectl get pod --watch               # live view of pod status
kubectl get services
kubectl get all
kubectl describe pod POD_NAME
kubectl describe service SERVICE_NAME # endpoints should be the pod IPs
kubectl get deployment DEPLOYMENT_NAME -o yaml   # current config and status, as stored in etcd
```

Create, edit and delete:

```bash
kubectl create deployment NAME --image=IMAGE   # add --dry-run to preview
kubectl edit deployment DEPLOYMENT_NAME
kubectl delete deployment DEPLOYMENT_NAME
```

Logs and debugging:

```bash
kubectl logs POD_NAME
kubectl exec -it POD_NAME -- bin/bash          # open a terminal inside the container
```

Config files:

```bash
kubectl apply -f nginx-deployment.yml
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

Secrets, namespaces and ingress:

```bash
echo -n 'username' | base64                    # encode a secret value
kubectl create namespace my-namespace
kubectl get ingress -n kubernetes-dashboard
kubectl describe ingress dashboard-ingress -n kubernetes-dashboard
```

Minikube only:

```bash
minikube service SERVICE_NAME                  # open an external service in the browser
```

In Minikube an external service does not get an external IP, so use `minikube service`.

---

## 6. Configuration Files (YAML)

### 6.1 Structure

Every config file has 3 parts:

1. **metadata**: name and labels.
2. **spec**: the attributes for that kind (Deployment, Service and so on).
3. **status**: written automatically by Kubernetes. You do not write it.

The first two lines name the API version and the kind:

```yaml
apiVersion: apps/v1
kind: Deployment      # could also be Service, ConfigMap, Secret...
```

A Deployment has a `template`. The template has its own `metadata` and `spec`, and they describe the **pods**:

```yaml
spec:
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

### 6.2 Labels and selectors

- `metadata` holds **labels**.
- `spec` holds **selectors**.
- Pods get their label from the Deployment's pod template.
- The Deployment's selector finds its pods by that label.

```yaml
spec:
  selector:
    matchLabels:
      app: nginx
```

Kubernetes does not need the labels to be identical. It only needs every label in the selector to be present on the pod.

A Service finds the pods in the same way:

```yaml
# Service
spec:
  selector:
    app: nginx
```

```text
Deployment  (template label: app: nginx) --creates--> Pods (label app: nginx)
Service     (selector: app: nginx)       --routes to--> the same Pods
```

### 6.3 Ports

- The Service `port` is where the Service itself listens.
- The Service `targetPort` must match the pod's `containerPort` in the Deployment.

```yaml
ports:
  - protocol: TCP
    port: 80          # the Service listens here
    targetPort: 8080  # the pod's containerPort
```

---

## 7. Web App Setup: MongoDB + Mongo Express

### 7.1 Plan and folder layout

1. Create a **MongoDB** Deployment and an **internal** Service. Only components inside the cluster can reach it.
2. Create a **Mongo Express** Deployment. It needs the database URL and the credentials as environment variables.
3. Create a **ConfigMap** with the database URL.
4. Create a **Secret** with the database credentials.
5. Reference the ConfigMap and the Secret inside the deployments.
6. Create a Service for Mongo Express and an Ingress, so the browser can reach it.

```text
web setup/
  secrets/secrets-mongo.yaml
  configmap/configmap-mongo.yaml
  deployments/deployment-mongo.yaml
  deployments/deployment-express.yaml
  services/service-mongo.yaml
  services/service-express.yaml
  ingress/mongo-express-ingress.yaml
  ingress/dashboard-ingress.yaml
```

```text
Browser --> Ingress (express.com) --> mongodb-express-service (8081)
        --> mongo-express pod --> mongodb-service (27017) --> mongodb pod
                 reads: ConfigMap (database_url), Secret (username, password)
```

### 7.2 Secret

Secret values must be base64 encoded. Create the Secret before the deployments that use it.

```bash
echo -n 'username' | base64    # dXNlcm5hbWU=
echo -n 'password' | base64    # cGFzc3dvcmQ=
```

`secrets/secrets-mongo.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongo-secret
type: Opaque
data:
  mongo-root-username: dXNlcm5hbWU=
  mongo-root-password: cGFzc3dvcmQ=
  mongo-url: bW9uZ29kYjovL3VzZXJuYW1lOnBhc3N3b3JkQG1vbmdvZGItc2VydmljZToyNzAxNy8=   # mongodb://username:password@mongodb-service:27017/
```

- `username` and `password` here are placeholder demo values. Use real credentials and keep them out of Git.
- base64 is only encoding, not encryption. Anyone who can read the Secret can decode it.
- The `mongo-url` key is not used by the deployments below.

Commands (run from the `web setup` folder):

```bash
kubectl apply -f secrets/secrets-mongo.yaml            # create or update
kubectl get secret                                     # list secrets
kubectl describe secret mongo-secret                   # shows the keys, not the values
kubectl get secret mongo-secret -o yaml                # shows the base64 values
echo 'dXNlcm5hbWU=' | base64 --decode                  # decode a value: username

kubectl delete -f secrets/secrets-mongo.yaml           # remove using the file
kubectl delete secret mongo-secret                     # remove by name
```

### 7.3 ConfigMap

`configmap/configmap-mongo.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mongodb-configmap
data:
  database_url: mongodb-service     # the name of the MongoDB Service
```

The value is the **Service name**. Inside the cluster a Service name works as a host name.

Commands:

```bash
kubectl apply -f configmap/configmap-mongo.yaml        # create or update
kubectl get configmap                                  # list configmaps
kubectl describe configmap mongodb-configmap           # shows the data
kubectl get configmap mongodb-configmap -o yaml

kubectl delete -f configmap/configmap-mongo.yaml       # remove using the file
kubectl delete configmap mongodb-configmap             # remove by name
```

### 7.4 MongoDB deployment and internal service

`deployments/deployment-mongo.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:4.4
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-root-password
```

`services/service-mongo.yaml` has no `type`, so it is the default `ClusterIP` (internal):

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb          # matches the pod label
  ports:
    - protocol: TCP
      port: 27017         # the Service port
      targetPort: 27017   # the pod's containerPort
```

Commands for the MongoDB Deployment and its Service:

```bash
kubectl apply -f deployments/deployment-mongo.yaml
kubectl apply -f services/service-mongo.yaml

kubectl get deployment
kubectl get pod -l app=mongodb                         # pods with the label app=mongodb
kubectl describe deployment mongodb-deployment
kubectl logs deployment/mongodb-deployment
kubectl get service
kubectl describe service mongodb-service               # endpoints = the MongoDB pod IP

kubectl delete -f services/service-mongo.yaml
kubectl delete -f deployments/deployment-mongo.yaml    # also removes its ReplicaSet and pods
kubectl delete deployment mongodb-deployment           # remove by name
kubectl delete service mongodb-service
```

### 7.5 Mongo Express deployment and service

`deployments/deployment-express.yaml` reads the credentials from the Secret and the database URL from the ConfigMap:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo-express
  labels:
    app: mongo-express
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo-express
  template:
    metadata:
      labels:
        app: mongo-express
    spec:
      containers:
      - name: mongo-express
        image: mongo-express:0.54.0
        ports:
        - containerPort: 8081
        env:
        - name: ME_CONFIG_MONGODB_ADMINUSERNAME
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-root-username
        - name: ME_CONFIG_MONGODB_ADMINPASSWORD
          valueFrom:
            secretKeyRef:
              name: mongo-secret
              key: mongo-root-password
        - name: ME_CONFIG_MONGODB_SERVER
          valueFrom:
            configMapKeyRef:
              name: mongodb-configmap
              key: database_url
```

`services/service-express.yaml` is now internal (`ClusterIP`), and the Ingress gives it the outside access:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongodb-express-service
spec:
  selector:
    app: mongo-express
  type: ClusterIP
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
```

Before the Ingress was added, this Service was external:

```yaml
# type: LoadBalancer
# ports:
#   - protocol: TCP
#     port: 8081
#     targetPort: 8081
#     nodePort: 30000      # nodePort range starts at 30000
```

An external Service uses `type: LoadBalancer` with a `nodePort` (30000 and above). An internal Service uses the default `ClusterIP` and has no `nodePort`.

Commands for the Mongo Express Deployment and its Service:

```bash
kubectl apply -f deployments/deployment-express.yaml
kubectl apply -f services/service-express.yaml

kubectl get pod -l app=mongo-express
kubectl describe deployment mongo-express
kubectl logs deployment/mongo-express
kubectl describe service mongodb-express-service

kubectl delete -f services/service-express.yaml
kubectl delete -f deployments/deployment-express.yaml
```

### 7.6 Apply everything and test

Run the commands from the `web setup` folder. The Secret and ConfigMap must exist before the deployments that reference them, so apply them in this order:

```bash
kubectl apply -f secrets/secrets-mongo.yaml
kubectl apply -f configmap/configmap-mongo.yaml
kubectl apply -f deployments/deployment-mongo.yaml
kubectl apply -f services/service-mongo.yaml
kubectl apply -f deployments/deployment-express.yaml
kubectl apply -f services/service-express.yaml
kubectl apply -f ingress/mongo-express-ingress.yaml

kubectl get all
kubectl get pod --watch
kubectl describe service mongodb-service    # endpoints should show the MongoDB pod IP
kubectl logs POD_NAME
```

To open it in the browser, set up the Ingress in section 9.3 and 9.4. If you use the older external Service (`LoadBalancer`) instead, run:

```bash
minikube service mongodb-express-service
```

Applying a whole folder also works, but the files go in alphabetical order, so the deployments start before the Secret exists. The pods then fail for a moment and recover once the Secret is created. Applying one file at a time, as above, avoids that.

```bash
kubectl apply -f deployments/                  # every file in one folder
kubectl apply -f . -R                          # every file in all sub-folders
```

### 7.7 Change and remove

Change a resource: edit the YAML file and apply it again. Kubernetes updates only what changed.

```bash
kubectl apply -f deployments/deployment-express.yaml
kubectl rollout status deployment mongo-express
```

Environment variables are read only when a pod starts. After you change a ConfigMap or Secret, restart the pods that use it:

```bash
kubectl rollout restart deployment mongo-express
kubectl rollout status deployment mongo-express
```

Remove the whole web app. Delete in the reverse order of creation:

```bash
kubectl delete -f ingress/mongo-express-ingress.yaml
kubectl delete -f services/service-express.yaml
kubectl delete -f deployments/deployment-express.yaml
kubectl delete -f services/service-mongo.yaml
kubectl delete -f deployments/deployment-mongo.yaml
kubectl delete -f configmap/configmap-mongo.yaml
kubectl delete -f secrets/secrets-mongo.yaml

kubectl get all                                # check what is left
```

- Deleting a Deployment also deletes its ReplicaSet and pods.
- Deleting the Secret or ConfigMap while pods still use them breaks any new pod that starts.
- The MongoDB deployment has no Volume, so its data is lost when the pod is removed.
- If you delete only a pod, the Deployment starts a new one, so to stop an app delete the Deployment.

```bash
kubectl delete pod POD_NAME                    # the Deployment creates a replacement
kubectl scale deployment mongo-express --replicas=0    # stop the pods but keep the Deployment
kubectl scale deployment mongo-express --replicas=1    # start again
```

---

## 8. Namespaces

A namespace is a virtual cluster inside a cluster. One cluster can have many namespaces, and they organize resources.

Default namespaces:

| Namespace | Purpose |
|---|---|
| `kube-system` | System processes. Not for our use. |
| `kubernetes-dashboard` | The dashboard (specific to Minikube). |
| `kube-public` | Public cluster info, for example `kubectl cluster-info`. |
| `kube-node-lease` | Node heartbeat info. Each node gets its own object. |
| `default` | Where your resources go if you do not set a namespace. |

```bash
kubectl create namespace my-namespace
```

A namespace can also be created with a config file:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
```

Commands:

```bash
kubectl apply -f namespace.yaml
kubectl get namespaces

kubectl apply -f deployments/deployment-mongo.yaml -n my-namespace   # create in a namespace
kubectl get all -n my-namespace                                      # list inside a namespace
kubectl get pod --all-namespaces                                     # list in every namespace

kubectl config set-context --current --namespace=my-namespace        # change the default namespace
kubectl delete namespace my-namespace                                # deletes EVERYTHING inside it
```

A resource can also name its namespace in `metadata`, so the `-n` flag is not needed:

```yaml
metadata:
  name: mongodb-configmap
  namespace: my-namespace
```

Why use namespaces:
1. **Group resources**: for example one namespace for the database and one for monitoring.
2. **Avoid name conflicts**: two teams can each have a deployment with the same name if they use separate namespaces. Otherwise one overrides the other.
3. **Share resources**: staging and development can share Nginx or an Elastic stack.
4. **Blue/green deployments**: blue is the current production, green is the next production.
5. **Access and resource limits**: limit or assign resources per namespace.

Characteristics:
- A ConfigMap or Secret in one namespace cannot be used from another namespace. Each namespace needs its own.
- A **Service** can be referenced from another namespace by adding the namespace to its name:

  ```yaml
  db_url: mysql-service.database     # "database" is the namespace
  ```

- Some components cannot be put inside a namespace. A Volume, for example, is available to the whole cluster.
- `kubens` changes the default namespace.

---

## 9. Ingress

### 9.1 What it does

Without Ingress, an external Service exposes the app by IP and port. With Ingress, the Services become internal and requests come in through the Ingress using a readable host name.

```text
Request --> Ingress --> internal Service --> Pod
```

The Service type changes too: an internal Service has the default type `ClusterIP` and no `nodePort`. An external Service has `type: LoadBalancer`.

### 9.2 Ingress controller

An Ingress YAML file is not enough on its own. The cluster also needs an **Ingress controller** pod.

- The controller reads the Ingress rules, evaluates them and manages the redirections.
- It is the entry point to the cluster.
- There are many third-party implementations. The Kubernetes one is the **Nginx Ingress controller**.
- The cluster also needs an entry point from outside. This can be software or hardware, or in the cloud (AWS, Azure) their load balancer.
- A **proxy server** can fill this role: a separate server with a public IP and open ports.

```text
Request --> proxy server (public IP) --> Ingress controller --> Service --> Pod
```

The `host` in a rule must be a valid domain. The domain name must point to the IP address of the entry point node.

### 9.3 Setup in Minikube

1. Install the Ingress controller:

```bash
minikube addons enable ingress
kubectl get pod -n kube-system
```

2. Create the Ingress rule (section 9.4), then check it:

```bash
kubectl get ingress -n kubernetes-dashboard
# NAME                CLASS    HOSTS           ADDRESS        PORTS   AGE
# dashboard-ingress   <none>   dashboard.com   192.168.49.2   80      9m4s
```

3. Map the domain to an IP in the Windows hosts file (`C:\Windows\System32\drivers\etc\hosts`):

```text
127.0.0.1 dashboard.com
```

`192.168.49.2 dashboard.com` did not work in this setup, because the Minikube IP is not reachable from the host. Use `127.0.0.1` and one of these:

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 80:80
minikube tunnel
```

For the web app use `express.com` in the hosts file in the same way.

### 9.4 Ingress rules for the web app

`ingress/mongo-express-ingress.yaml` sends `express.com` to the Mongo Express Service:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mongo-express-ingress
spec:
  rules:
    - host: express.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: mongodb-express-service
                port:
                  number: 8081
```

`ingress/dashboard-ingress.yaml` exposes the Kubernetes dashboard. The Ingress must be in the same namespace as the Service it points to:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
  namespace: kubernetes-dashboard
spec:
  rules:
  - host: dashboard.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kubernetes-dashboard
            port:
              number: 80
```

The `http:` key here is not the same as the `http://` in a URL. It is just the protocol the incoming request to the rule uses (the second routing step).

Commands:

```bash
kubectl apply -f ingress/mongo-express-ingress.yaml
kubectl apply -f ingress/dashboard-ingress.yaml          # has its own namespace in the file

kubectl get ingress
kubectl get ingress -n kubernetes-dashboard
kubectl describe ingress mongo-express-ingress           # rules, backends, events
kubectl describe ingress dashboard-ingress -n kubernetes-dashboard

kubectl delete -f ingress/mongo-express-ingress.yaml
kubectl delete ingress dashboard-ingress -n kubernetes-dashboard
```

### 9.5 Multiple paths and hosts

Several paths on the same host:

```yaml
spec:
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /analytics
        pathType: Prefix
        backend:
          service:
            name: analytics-service
            port:
              number: 3000
      - path: /shopping
        pathType: Prefix
        backend:
          service:
            name: shopping-service
            port:
              number: 8080
```

Several sub-domains:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
spec:
  rules:
  - host: analytics.myapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: analytics-service
            port:
              number: 3000
  - host: shopping.myapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: shopping-service
            port:
              number: 8080
```

### 9.6 Default backend

If a request does not match any rule, the **default backend** handles it. Use it to show a custom error message or a "page not found" page.

```bash
kubectl describe ingress dashboard-ingress -n kubernetes-dashboard    # shows the default backend
```

For a custom page, create an internal Service named `default-http-backend` on port 80. (This name applies to the older Nginx setup. Newer controllers can also set a `defaultBackend` in the Ingress.)

### 9.7 TLS certificate

Add a `tls` section to the Ingress and point it at a Secret:

```yaml
spec:
  tls:
  - hosts:
    - myapp.com
    secretName: myapp-secret-tls
  rules:
  - host: myapp.com
    # ...
```

The Secret that holds the certificate:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secret-tls
  namespace: default
data:
  tls.crt: <base64 encoded certificate>
  tls.key: <base64 encoded key>
type: kubernetes.io/tls
```

Rules:
1. The data keys must be named exactly `tls.crt` and `tls.key`.
2. The values are the file **contents** (base64 encoded), not file paths.
3. The Secret must be in the same namespace as the Ingress.

---

## 10. Helm

Helm is the package manager for Kubernetes. It packages YAML files and distributes them through public and private repositories.

**Helm charts**:
1. A chart is a bundle of YAML files.
2. You can create your own charts.
3. Push them to a Helm repository.
4. Download and use existing charts.

Helm is also a **templating engine**:
- Define one template (blueprint) for all the microservices.
- Values that change are placeholders in the template.
- A `values.yaml` file holds those values.
- Useful for CI/CD, and for deploying the same application to different environments.

```text
template (deployment.yaml with {{ placeholders }})  +  values.yaml  -->  final YAML  -->  cluster
```
