# Kubernetes Notes (Refresher)


---

## 1. What is Kubernetes?

Kubernetes is an open-source container orchestration platform used to deploy, manage, and scale applications across a cluster of machines. It provides a way to run distributed systems in a reliable and automated manner by taking care of tasks such as scheduling, failover, scaling, service discovery, and rolling updates.

At a high level, Kubernetes acts as an intelligent control plane for modern applications. Instead of manually starting containers, assigning them to servers, or restarting them when they fail, Kubernetes continuously observes the desired state of the system and works to maintain it. If a container crashes, if traffic increases, or if a new version of an application needs to be deployed, Kubernetes can respond automatically.

The central problem Kubernetes solves is the complexity of operating applications at scale. As systems grow, the number of containers, dependencies, and infrastructure decisions becomes too large to manage manually. Kubernetes brings structure to this process by allowing engineers to define the desired state of an application and letting the platform handle the implementation details.

In practice, Kubernetes helps organizations achieve:

1. **High availability** — applications remain accessible even when individual components fail.
2. **Scalability** — workloads can be increased or decreased dynamically based on demand.
3. **Resilience** — self-healing behavior helps recover from failures without manual intervention.
4. **Efficient resource use** — computing resources can be distributed and utilized more effectively across machines.
5. **Simplified operations** — deployment, updates, rollback, and configuration management become more consistent and repeatable.

In short, Kubernetes is not just a tool for launching containers; it is a platform for managing the lifecycle of cloud-native applications in a dependable, automated, and scalable way.

---

## 2. Core Components

| Component | What it is |
|---|---|
| **Node** | A server/machine in the cluster. Can be a master node or worker node. |
| **Pod** | Smallest deployable unit. Wraps one or more containers, giving them a shared IP, network, and storage. Each pod gets its own IP. If a pod crashes, a new one is created with a **new** IP. |
| **Service** | A static/permanent IP address for a pod (or group of pods). If the pod dies, the Service's IP stays the same. Also acts as a **load balancer** across multiple pods of the same app. |
| **Ingress** | Provides a human-readable URL instead of an IP. Request → Ingress → Service → Pod. |
| **ConfigMap** | Stores non-sensitive configuration data (e.g. a database URL), so you don't need to rebuild your app image every time config changes. **Cannot** store usernames/passwords. |
| **Secret** | Like a ConfigMap, but for sensitive data (usernames, passwords). Values are base64 encoded. |
| **Volume** | Persistent storage. Lives **outside** the Kubernetes cluster. |
| **Deployment** | A blueprint for pods. Declares how many replicas to run and lets you scale up/down. A wrapper around pods (and manages a ReplicaSet underneath). |
| **StatefulSet** | Like a Deployment, but keeps database reads/writes in sync between replicas. Needed because a plain Deployment would cause data sync issues for databases. |

### Service details
- **External service**: makes an app accessible through the browser
- **Internal service**: for things like databases (no external access)
- Load-balances requests to whichever pod is least busy

---

## 3. Node Architecture

### Worker Node
Worker nodes do the actual work. Each one runs 3 processes to manage its pods:

1. **Container runtime** (e.g. Docker) — actually runs the containers
2. **Kubelet** — schedules pods/containers on the node; interfaces between the container runtime and the pods
3. **Kube-proxy** — forwards requests from Services to the right Pods

### Master Node
Controls the cluster state and the worker nodes. Runs 4 processes (in production, there can be multiple master nodes):

1. **API Server** — the cluster's gateway. All clients (kubectl, UI/dashboard, etc.) talk to it. Handles authentication, validates requests (new app, new service, new pod), then forwards them.
2. **Scheduler** — once the API server validates a request, the scheduler decides which worker node the new pod should run on (usually the least busy one).
3. **Controller Manager** — detects state changes (e.g. a pod crashing) and tries to recover cluster state by asking the scheduler to reschedule.
4. **etcd** — key-value store; the "brain" of the cluster. Every change is saved here. This is how the scheduler and controller manager know the current state of resources.

> Master node processes need fewer resources than worker nodes.

---

## 4. Minikube & kubectl

- **Minikube**: runs a single-node cluster locally (master + worker processes on one node), inside a virtual machine, with Docker pre-installed. Good for local testing since you can't run a full multi-node cluster locally.
- **kubectl**: command-line tool used to talk to the cluster's API server.
- **Production cluster** = multiple master nodes + multiple worker nodes.

```bash
# Check versions / cluster status
minikube version
kubectl version --client
minikube start
minikube status
kubectl get nodes
```

---

## 5. Layers: Deployment → ReplicaSet → Pod

- We don't create Pods directly — we create a **Deployment** (an abstraction over Pods).
- Kubernetes automatically creates a **ReplicaSet** between the Deployment and the Pods.

```
Deployment  → manages →  ReplicaSet
ReplicaSet  → manages →  all replicas of a Pod
Pod         → wraps   →  container(s)
```

---

## 6. kubectl Commands (grouped by topic)

### General / resource inspection
```bash
kubectl get nodes
kubectl get pod
kubectl get pod -o wide          # more output, e.g. shows pod IPs
kubectl get pod --watch          # live-watch pod status
kubectl get services
kubectl get all                  # pods, services, deployments, replicasets
kubectl describe pod POD_NAME
kubectl describe service SERVICE_NAME   # check the service's endpoints (should be the pod's IP)
kubectl get deployment DEPLOYMENT_NAME -o yaml   # full config as stored in etcd
```

### Creating / editing deployments
```bash
kubectl create deployment NAME --image=IMAGE [--dry-run] [options]
kubectl edit deployment DEPLOYMENT_NAME
kubectl delete deployment DEPLOYMENT_NAME
```

### Logs & debugging
```bash
kubectl logs POD_NAME
kubectl exec -it POD_NAME -- bin/bash    # open a shell inside the container
```

### Applying / deleting config files
```bash
kubectl apply -f nginx-deployment.yml
kubectl apply -f config-file.yml
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

### Secrets (base64 encoding)
```bash
echo -n 'username' | base64
```

### Minikube-specific: accessing an external service
Minikube doesn't auto-assign an external IP for external services, so use:
```bash
minikube service SERVICE_NAME
```

---

## 7. Kubernetes Configuration File Structure

Every config file has 3 parts:

1. **metadata** — name, labels
2. **specification (spec)** — attributes specific to the kind (Deployment, Service, etc.)
3. **status** — automatically generated and updated by Kubernetes (not written by us)

First two lines of every file:
```yaml
apiVersion: apps/v1
kind: Deployment   # could also be Service, ConfigMap, Secret, etc.
```

### Deployment: how pods are configured
A Deployment's `spec` contains a `template`, which itself has its own `metadata` and `spec` — this describes the **pods** it creates:
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

### Labels & Selectors
- **metadata** contains **labels**
- **spec** contains **selectors**
- Pods get their label from the Deployment's pod template.
- The Deployment's selector matches pods by that label.

```yaml
spec:
  selector:
    matchLabels:
      app: nginx
```

> Kubernetes does **not** require labels to be identical — it only requires that all labels in the selector are present on the Pod.

A Deployment also has its own top-level label (e.g. `app: nginx`). A **Service** connects to the Deployment's pods using that same label:
```yaml
# service config
spec:
  selector:
    app: nginx
```

### Ports (Service vs Pod)
- The Service's `port` is where the *Service itself* is accessible.
- The Service's `targetPort` must match the Pod's `containerPort` (defined in the Deployment).

```yaml
ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

---

## 8. Example: Web App Setup (MongoDB + Mongo Express)

**Plan:**
1. Create a MongoDB Deployment + an **internal** Service (no external access — only other components in the cluster can reach it).
2. Create a Deployment for Mongo Express, passing the database URL and credentials in as environment variables.
3. Create a **ConfigMap** holding the database URL.
4. Create a **Secret** holding the database credentials.
5. Reference both the ConfigMap and Secret inside the Mongo Express Deployment.
6. Create an **external** Service for Mongo Express so it's reachable from the browser.

---

## 9. Namespaces

A namespace is a **virtual cluster inside a cluster** — used to organize resources. A cluster can contain multiple namespaces.

### Default namespaces
| Namespace | Purpose |
|---|---|
| `kube-system` | System processes — not for our use |
| `kubernetes-dashboard` | Minikube-specific dashboard resources |
| `kube-public` | Public, readable cluster info (e.g. `kubectl cluster-info`) |
| `kube-node-lease` | Heartbeat info for each node |
| `default` | Where your resources go if you don't specify a namespace |

```bash
kubectl create namespace my-namespace
```
Namespaces can also be created via a config file.

### Why use namespaces?
1. **Grouping** — e.g. separate namespaces for database, monitoring, etc.
2. **Avoiding name collisions** — two teams can have a deployment with the same name without conflict if they're in different namespaces.
3. **Resource sharing** — e.g. staging and development can share the same Nginx or Elastic Stack.
4. **Blue/green deployments** — blue = current production, green = next production.
5. **Access & resource limits** — you can limit/assign resources per namespace.

### Characteristics
- A ConfigMap/Secret in one namespace (e.g. Team A) **cannot** be referenced by resources in another namespace (e.g. Team B) — each needs its own.
- A **Service** can be referenced across namespaces using this format:
  ```yaml
  db_url: mysql-service.database   # "database" is the namespace
  ```
- Some components **cannot** be scoped to a single namespace — e.g. **Volumes** are accessible cluster-wide.
- Use the `kubens` tool to quickly switch your default namespace.

---

## 10. Ingress

*(Not covered yet — to be filled in later.)*
