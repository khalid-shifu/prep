# Docker Interview Questions (Senior Backend: Java, Spring Boot, Microservices)

41 questions in four topic groups. Inside each group, questions go from Basic to Intermediate to Advanced, and every question heading shows its level.
Each question has: a short answer, an explanation, code or a diagram, and likely follow-up questions.

Terms and examples match `DOCKER-README.md` (same folder).

Notes on versions: version-specific claims are marked. Commands are shown as examples; they are not output from a run.

## Index

**1. Images and Containers**

- Basic:
  - [Q1. What is the difference between a container and a virtual machine?](#q1-basic-what-is-the-difference-between-a-container-and-a-virtual-machine)
  - [Q2. What is the difference between a Docker image and a container?](#q2-basic-what-is-the-difference-between-a-docker-image-and-a-container)
  - [Q3. Explain the container lifecycle and the basic commands (run, ps, stop, kill, rm).](#q3-basic-explain-the-container-lifecycle-and-the-basic-commands-run-ps-stop-kill-rm)
  - [Q4. How do you debug a container? (logs, exec, inspect)](#q4-basic-how-do-you-debug-a-container-logs-exec-inspect)
  - [Q5. What are image tags, and why is the `latest` tag a problem?](#q5-basic-what-are-image-tags-and-why-is-the-latest-tag-a-problem)
- Intermediate:
  - [Q6. What are restart policies?](#q6-intermediate-what-are-restart-policies)
  - [Q7. What is a container registry, and what tagging strategy do you use?](#q7-intermediate-what-is-a-container-registry-and-what-tagging-strategy-do-you-use)
- Advanced:
  - [Q8. What is a Docker image's filesystem and how does copy-on-write work?](#q8-advanced-what-is-a-docker-images-filesystem-and-how-does-copy-on-write-work)
  - [Q9. How do namespaces and cgroups make containers work?](#q9-advanced-how-do-namespaces-and-cgroups-make-containers-work)

**2. Dockerfile and Builds**

- Basic:
  - [Q10. What are image layers, and how does Docker layer caching work?](#q10-basic-what-are-image-layers-and-how-does-docker-layer-caching-work)
  - [Q11. What do the main Dockerfile instructions do (FROM, WORKDIR, COPY, RUN, USER, EXPOSE, CMD)?](#q11-basic-what-do-the-main-dockerfile-instructions-do-from-workdir-copy-run-user-expose-cmd)
  - [Q12. What is the difference between COPY and ADD?](#q12-basic-what-is-the-difference-between-copy-and-add)
  - [Q13. What is the difference between CMD and ENTRYPOINT?](#q13-basic-what-is-the-difference-between-cmd-and-entrypoint)
  - [Q14. What is the difference between ENV and ARG?](#q14-basic-what-is-the-difference-between-env-and-arg)
  - [Q15. What is `.dockerignore` and why is it important?](#q15-basic-what-is-dockerignore-and-why-is-it-important)
- Intermediate:
  - [Q16. What are multi-stage builds and why use them?](#q16-intermediate-what-are-multi-stage-builds-and-why-use-them)
  - [Q17. How do you reduce Docker image size?](#q17-intermediate-how-do-you-reduce-docker-image-size)
  - [Q18. What is HEALTHCHECK in a Dockerfile and how is it different from a Kubernetes probe?](#q18-intermediate-what-is-healthcheck-in-a-dockerfile-and-how-is-it-different-from-a-kubernetes-probe)
  - [Q19. How do you containerize a Spring Boot application? (production Dockerfile)](#q19-intermediate-how-do-you-containerize-a-spring-boot-application-production-dockerfile)
- Advanced:
  - [Q20. What are layered jars in Spring Boot, and why do they improve Docker builds?](#q20-advanced-what-are-layered-jars-in-spring-boot-and-why-do-they-improve-docker-builds)
  - [Q21. What is PID 1 in a container, and how do you get graceful shutdown?](#q21-advanced-what-is-pid-1-in-a-container-and-how-do-you-get-graceful-shutdown)

**3. Networking, Volumes and Compose**

- Basic:
  - [Q22. What does EXPOSE do, and how does port publishing (`-p`) work?](#q22-basic-what-does-expose-do-and-how-does-port-publishing--p-work)
  - [Q23. What is the difference between volumes and bind mounts? How do you persist data?](#q23-basic-what-is-the-difference-between-volumes-and-bind-mounts-how-do-you-persist-data)
  - [Q24. What are Docker networking modes (bridge, host, none)?](#q24-basic-what-are-docker-networking-modes-bridge-host-none)
- Intermediate:
  - [Q25. How does container-to-container networking and DNS work? Why use user-defined networks?](#q25-intermediate-how-does-container-to-container-networking-and-dns-work-why-use-user-defined-networks)
  - [Q26. What is Docker Compose and how do you write a compose file for a Spring Boot app with a database?](#q26-intermediate-what-is-docker-compose-and-how-do-you-write-a-compose-file-for-a-spring-boot-app-with-a-database)
  - [Q27. What does `depends_on` do, and why do you need healthchecks?](#q27-intermediate-what-does-depends_on-do-and-why-do-you-need-healthchecks)
  - [Q28. How do you manage environments in Compose (env files, profiles, override files)?](#q28-intermediate-how-do-you-manage-environments-in-compose-env-files-profiles-override-files)
- Advanced:
  - [Q29. A port is already in use or a container is not reachable. How do you debug?](#q29-advanced-a-port-is-already-in-use-or-a-container-is-not-reachable-how-do-you-debug)
  - [Q30. A container has DNS or network problems (cannot resolve a name or reach another service). How do you debug?](#q30-advanced-a-container-has-dns-or-network-problems-cannot-resolve-a-name-or-reach-another-service-how-do-you-debug)

**4. Production, Security and Kubernetes Basics**

- Intermediate:
  - [Q31. How do you limit CPU and memory for a container?](#q31-intermediate-how-do-you-limit-cpu-and-memory-for-a-container)
  - [Q32. How do you pass configuration and secrets to a container?](#q32-intermediate-how-do-you-pass-configuration-and-secrets-to-a-container)
  - [Q33. Docker Compose vs Docker Swarm vs Kubernetes: what is the difference?](#q33-intermediate-docker-compose-vs-docker-swarm-vs-kubernetes-what-is-the-difference)
  - [Q34. A container exits immediately after starting. How do you debug it?](#q34-intermediate-a-container-exits-immediately-after-starting-how-do-you-debug-it)
- Advanced:
  - [Q35. How does the JVM behave in a container, and how do you set memory correctly?](#q35-advanced-how-does-the-jvm-behave-in-a-container-and-how-do-you-set-memory-correctly)
  - [Q36. How do you secure Docker images and containers?](#q36-advanced-how-do-you-secure-docker-images-and-containers)
  - [Q37. How would you build and push Docker images in a Jenkins pipeline?](#q37-advanced-how-would-you-build-and-push-docker-images-in-a-jenkins-pipeline)
  - [Q38. A container keeps getting OOMKilled. How do you investigate?](#q38-advanced-a-container-keeps-getting-oomkilled-how-do-you-investigate)
  - [Q39. The host disk is full because of Docker. How do you find and fix it?](#q39-advanced-the-host-disk-is-full-because-of-docker-how-do-you-find-and-fix-it)
  - [Q40. Kubernetes basics: what are a Pod, Deployment, Service and Ingress?](#q40-advanced-kubernetes-basics-what-are-a-pod-deployment-service-and-ingress)
  - [Q41. Kubernetes basics: ConfigMap, Secret, probes and HPA.](#q41-advanced-kubernetes-basics-configmap-secret-probes-and-hpa)

---

## 1. Images and Containers

### Q1. [Basic] What is the difference between a container and a virtual machine?

**Short answer:** A virtual machine (VM) runs a full guest operating system (OS) with its own kernel on virtual hardware. A container is a normal process on the host that is isolated by the host kernel and shares that kernel.

**Explanation:**

- The **kernel** is the core part of the OS. It manages memory, CPU, files and networking.
- A VM needs a **hypervisor** (software that creates virtual hardware, for example VMware, KVM, Hyper-V). Each VM boots its own OS. This takes minutes and gigabytes.
- A container does not boot an OS. It starts a process using the host kernel. This takes about a second and the image is usually tens or hundreds of MB.
- Because the kernel is shared, a Linux container needs a Linux kernel. On Windows and macOS, Docker Desktop runs a small Linux VM and puts the containers inside it.
- Isolation is weaker than a VM. A kernel bug can affect all containers on the host.

```text
   VIRTUAL MACHINES                       CONTAINERS
+-------+ +-------+ +-------+        +-------+ +-------+ +-------+
| App A | | App B | | App C |        | App A | | App B | | App C |
| Libs  | | Libs  | | Libs  |        | Libs  | | Libs  | | Libs  |
| Guest | | Guest | | Guest |        +-------+ +-------+ +-------+
|  OS   | |  OS   | |  OS   |        +---------------------------+
+-------+ +-------+ +-------+        |      Docker Engine        |
+---------------------------+        +---------------------------+
|        Hypervisor         |        |   Host OS (one kernel)    |
+---------------------------+        +---------------------------+
|   Host OS / Hardware      |        |         Hardware          |
+---------------------------+        +---------------------------+
```

| Item | VM | Container |
|---|---|---|
| Contains | Full OS plus app | App plus its libraries only |
| Kernel | Own kernel per VM | Shares the host kernel |
| Start time | Minutes | Seconds or less |
| Typical size | GBs | MBs to hundreds of MB |
| Isolation | Strong (hardware level) | Weaker (kernel level: namespaces and cgroups) |

**Follow-up questions:**

- **Can a container run a different OS than the host?** It can run a different Linux distribution (for example Alpine on an Ubuntu host) because only user-space files differ. It cannot run a different kernel type. A Linux container cannot run on a Windows kernel directly.
- **When would you still choose a VM?** When you need strong isolation between tenants, a different kernel, or a full OS (for example, running untrusted code).
- **Do containers and VMs work together?** Yes. In the cloud, containers usually run inside VMs (for example, Kubernetes worker nodes on EC2 instances).

---

### Q2. [Basic] What is the difference between a Docker image and a container?

**Short answer:** An image is a read-only package (files plus metadata) used to create containers. A container is a running (or stopped) instance of an image, with a thin writable layer on top.

**Explanation:**

- An image holds the app, libraries, and config. It does not run by itself and does not change.
- `docker run` creates a container from an image and starts it. You can create many containers from one image.
- Each container gets its own writable layer. Files written there are lost when the container is removed (see Q23 for how to keep data).
- An image is identified by a name and tag (`myapp:1.0`) and by a content hash called a **digest** (`sha256:...`).

```text
Image (read-only)            Containers (running instances)
+---------------+            +-------------------+
| myapp:1.0     |  --run-->  | container web-1   |  + own writable layer
| layers 1..n   |  --run-->  | container web-2   |  + own writable layer
+---------------+            +-------------------+
```

```bash
docker build \
  -t myapp:1.0 \
  .
# Builds an image named myapp with tag 1.0 from the Dockerfile in the current folder

docker run \
  -d \
  --name web-1 \
  -p 8080:8080 \
  myapp:1.0
# Prints the new container ID. A container now runs from the image.

docker run \
  -d \
  --name web-2 \
  -p 8081:8080 \
  myapp:1.0
# A second container from the same image, with its own writable layer
```

**Follow-up questions:**

- **What is the difference between `docker run`, `docker start` and `docker create`?** `create` makes a container but does not start it. `start` starts an existing container. `run` is create plus start in one step, and it always makes a new container.
- **If I change the image, do running containers change?** No. A container keeps using the image layers it started with. You must create a new container from the new image.
- **What is a dangling image?** An image with no tag (shown as `<none>`), usually left behind after a rebuild moved the tag to a newer image. `docker image prune` removes them.

---

### Q3. [Basic] Explain the container lifecycle and the basic commands (run, ps, stop, kill, rm).

**Short answer:** A container goes through created, running, paused, stopped (exited) and removed. `docker stop` sends `SIGTERM` and then `SIGKILL` after a timeout. `docker kill` sends `SIGKILL` immediately. A container stops when its main process (PID 1) exits.

```text
 docker create          docker start               docker stop / kill
 +---------+  ------->  +---------+  ---------->  +---------+
 | Created |            | Running |               | Exited  |
 +---------+            +---------+  <----------  +---------+
                          |    ^     docker start     |
              docker pause|    |docker unpause        | docker rm
                          v    |                      v
                        +---------+              +---------+
                        | Paused  |              | Removed |
                        +---------+              +---------+
```

```bash
docker ps
# Lists running containers

docker ps \
  -a
# Lists all containers including stopped ones

docker stop \
  backend
# Sends SIGTERM, waits 10 seconds (default), then sends SIGKILL

docker stop \
  -t 30 \
  backend
# Same, but waits 30 seconds before SIGKILL

docker kill \
  backend
# Sends SIGKILL immediately, no graceful shutdown

docker rm \
  backend
# Removes a stopped container

docker run \
  --rm \
  myapp:1.0
# Removes the container automatically when it exits
```

**Follow-up questions:**

- **Why do containers stop right after start?** The main process finished or crashed (see Q34).
- **What is exit code 137? 143? 0?** 137 = killed by `SIGKILL` (128 + 9), often OOM kill or `docker kill`. 143 = stopped by `SIGTERM` (128 + 15). 0 = exited normally.
- **`docker stop` takes exactly 10 seconds every time. Why?** The app does not handle `SIGTERM` (often because a shell is PID 1), so Docker waits the full timeout and then kills it. See Q21.
- **`docker pause` vs `docker stop`?** `pause` freezes processes using cgroups, keeping memory. `stop` ends the process.

---

### Q4. [Basic] How do you debug a container? (logs, exec, inspect)

**Short answer:** Use `docker logs` to read output, `docker exec` to run commands inside a running container, `docker inspect` to see its full configuration (IP, mounts, env, exit code), and `docker stats` for CPU and memory.

```bash
docker logs \
  --tail 100 \
  --follow \
  --timestamps \
  backend
# Prints the last 100 lines and keeps streaming new lines with timestamps

docker exec \
  -it \
  backend \
  sh
# Opens a shell inside the running container (use bash if the image has it)

docker exec \
  backend \
  env
# Prints the container's environment variables, then exits

docker inspect \
  --format '{{.State.ExitCode}} {{.State.OOMKilled}}' \
  backend
# Example output: 137 true   (exit code and whether the kernel killed it for memory)

docker inspect \
  --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' \
  backend
# Prints the container IP address

docker stats \
  --no-stream
# One snapshot of CPU, memory and network use for all running containers

docker top \
  backend
# Shows the processes running inside the container

docker cp \
  backend:/app/logs/app.log \
  ./app.log
# Copies a file out of the container to the host
```

**Explanation:**

- `docker logs` shows what the main process wrote to stdout and stderr. Apps in containers should log to stdout, not to files.
- Leaving an `exec` shell with `exit` does not stop the container. Exiting the main process does.
- `docker exec` works only on a running container. If it exits immediately, use `docker logs` and `docker inspect` first.
- Slim images may have no shell, `curl` or `ps`. Options: use `docker exec -u root` with a debug image, or `docker run --rm -it --network container:backend nicolaka/netshoot` to attach a tool-rich container to the same network namespace.

**Follow-up questions:**

- **The container has no shell. How do you debug?** Run a debug container that shares its network or process space (`--network container:<name>`, `--pid container:<name>`), or use `docker cp` and logs. Recent Docker versions also have `docker debug` (version-specific, Docker Desktop and subscription feature).
- **Where do the logs go on disk?** With the default `json-file` driver, they are JSON files under `/var/lib/docker/containers/<id>/`. Without rotation they can fill the disk (see Q39).
- **How do you limit log size?** Set `--log-opt max-size=10m --log-opt max-file=3` or the same in `/etc/docker/daemon.json`.

---

### Q5. [Basic] What are image tags, and why is the `latest` tag a problem?

**Short answer:** A tag is a human-readable label for an image version (`myapp:1.4.2`). `latest` is only the default tag used when you give no tag. It does not mean "newest" and does not update itself.

**Explanation:**

- Tags are **mutable**. Someone can push a different image under the same tag. So `myapp:latest` can mean different images on different days or on different servers.
- Problems with `latest`: builds are not reproducible, rollback is hard (what was the previous `latest`?), and nodes can run different versions.
- Better: use an immutable, unique tag such as the semantic version plus Git commit (`1.4.2-a1b2c3d`) or the build number.
- Strongest: pin by **digest**, which is the sha256 hash of the image content and cannot change.
- Pin base images too: `eclipse-temurin:17.0.11_9-jre-alpine` rather than `eclipse-temurin:17`.

```bash
docker tag \
  myapp:1.4.2 \
  registry.example.com/team/myapp:1.4.2-a1b2c3d
# Adds a second name (the same image, not a copy)

docker pull \
  registry.example.com/team/myapp@sha256:3f1a...e9
# Pulls by digest: always the exact same bytes (digest shortened here)

docker images \
  --digests
# Shows the digest of each local image
```

```yaml
# Kubernetes: avoid "latest" so rollbacks and rollouts are predictable
containers:
  - name: myapp
    image: registry.example.com/team/myapp:1.4.2-a1b2c3d
```

**Follow-up questions:**

- **Image name anatomy?** `[registry/]namespace/repository[:tag]`. If no registry is given, Docker Hub is used.
- **In Kubernetes, what is special about `latest`?** If the tag is `latest` (or missing), the default `imagePullPolicy` is `Always`. For other tags it is `IfNotPresent`.
- **Can one image have several tags?** Yes. A release is often tagged `1.4.2`, `1.4` and `1` together, plus a commit tag.

---

### Q6. [Intermediate] What are restart policies?

**Short answer:** A restart policy tells Docker whether to restart a container when it exits or when the Docker daemon starts. Options: `no`, `on-failure[:max]`, `always`, `unless-stopped`.

| Policy | Behavior |
|---|---|
| `no` (default) | Never restart automatically. |
| `on-failure[:N]` | Restart only if the exit code is not 0. Optional maximum of N attempts. |
| `always` | Always restart. Also starts after a daemon or host restart, even if you stopped it manually earlier (it restarts when the daemon restarts). |
| `unless-stopped` | Like `always`, but not if you stopped it manually. |

```bash
docker run \
  -d \
  --name backend \
  --restart unless-stopped \
  myapp:1.0
# Restarts after a crash or host reboot, but not after "docker stop"

docker update \
  --restart on-failure:5 \
  backend
# Changes the policy of an existing container
```

```yaml
services:
  backend:
    image: myapp:1.0
    restart: unless-stopped
```

**Explanation:**

- Docker waits longer between restarts (backoff, starting at 100 ms and doubling) to avoid a tight crash loop.
- A restart policy does not fix the root cause. A container in a crash loop shows `Restarting` in `docker ps`. Read `docker logs`.
- Restart policies apply to a single host. For rescheduling a failed container on another host, use an orchestrator.

**Follow-up questions:**

- **`always` vs `unless-stopped`?** If you `docker stop` a container with `always` and the daemon restarts, it starts again. With `unless-stopped` it stays stopped.
- **What is `CrashLoopBackOff` in Kubernetes?** The same idea: the container keeps failing and Kubernetes waits longer between restarts.

---

### Q7. [Intermediate] What is a container registry, and what tagging strategy do you use?

**Short answer:** A registry stores and serves images (Docker Hub, AWS ECR, GitHub Container Registry, Harbor). You `docker push` to it from CI and `docker pull` from servers. Tag each build with an immutable unique tag (version plus commit) and promote the same image between environments.

**Explanation:**

- Image name format: `registry/namespace/repository:tag`. Example for AWS ECR: `123456789012.dkr.ecr.eu-west-1.amazonaws.com/myapp:1.4.2-a1b2c3d`.
- Private registries need login. ECR uses a temporary token from the AWS CLI.
- Good tagging:
  - Unique tag per build: `<version>-<git short sha>` or build number. Never overwritten.
  - Optional moving tags (`1.4`, `main`) for convenience. Deployments should use the unique tag or digest.
  - Do not use `latest` for deployments (Q5).
- Enable image scanning and lifecycle rules (delete old images) in the registry to control cost and risk.

```bash
aws ecr get-login-password \
  --region eu-west-1 \
| docker login \
  --username AWS \
  --password-stdin 123456789012.dkr.ecr.eu-west-1.amazonaws.com
# Prints: Login Succeeded

docker build \
  -t 123456789012.dkr.ecr.eu-west-1.amazonaws.com/myapp:1.4.2-a1b2c3d \
  .
# Builds with the full registry name in the tag

docker push \
  123456789012.dkr.ecr.eu-west-1.amazonaws.com/myapp:1.4.2-a1b2c3d
# Uploads only the layers that the registry does not already have
```

**Follow-up questions:**

- **Why does a push upload only some layers?** Layers are stored by content hash. Layers the registry already has are skipped.
- **Docker Hub pull rate limits?** Docker Hub limits anonymous and free-tier pulls (version-specific; limits change). CI servers often use a registry mirror or cache, or log in, or copy base images to their own registry.
- **How to deploy without a registry?** `docker save` writes an image to a `.tar` file and `docker load` imports it, for example in air-gapped environments.

---

### Q8. [Advanced] What is a Docker image's filesystem and how does copy-on-write work?

**Short answer:** Docker uses a union filesystem (usually `overlay2` on Linux) to stack read-only image layers and one writable container layer. When a container changes a file that is in a lower layer, the file is first copied up into the writable layer (copy-on-write) and changed there.

**Explanation:**

- Reading a file: found by looking from the top layer down.
- Writing or editing a file from an image layer: the whole file is copied to the writable layer first. This is slow for large files.
- Deleting: a marker (whiteout) hides the file in the upper layer. The lower layer still has it.
- The writable layer is deleted with the container. For heavy writes (databases, logs, uploads) use volumes, which bypass the union filesystem and are faster.

```text
+--------------------------------+
| Container layer  (read-write)  |  <- changes, new files; removed with container
+--------------------------------+
| Layer 4: COPY app.jar          |  read-only  \
| Layer 3: RUN adduser ...       |  read-only   | shared by all containers
| Layer 2: JRE                   |  read-only   | from the same image
| Layer 1: Base OS               |  read-only  /
+--------------------------------+
```

**Follow-up questions:**

- **Why are volumes better than the container layer for databases?** No copy-up cost and no union filesystem overhead, and data survives container removal.
- **Where is image data stored?** On Linux, under `/var/lib/docker` (for example `/var/lib/docker/overlay2`). Check usage with `docker system df`.
- **Why do two images from the same base use less disk than the sum of their sizes?** Shared layers are stored once.

---

### Q9. [Advanced] How do namespaces and cgroups make containers work?

**Short answer:** A container is a normal Linux process. **Namespaces** limit what the process can see (its own process list, network, filesystem, hostname, users). **cgroups** (control groups) limit what it can use (CPU, memory, disk I/O, process count).

**Explanation:**

| Namespace | What it isolates |
|---|---|
| `pid` | Process IDs. The container sees its own process tree, where the app is PID 1. |
| `net` | Network interfaces, IP addresses, routing tables, ports. |
| `mnt` | Mount points: the container sees its own root filesystem. |
| `uts` | Hostname. |
| `ipc` | Shared memory and message queues. |
| `user` | User and group IDs (root in the container can map to a non-root user on the host). Not enabled by default. |

- **cgroups** enforce limits: `--memory`, `--cpus`, `--pids-limit`, block I/O. They also give the numbers that `docker stats` reports.
- Other security layers: Linux **capabilities** (split root powers into small pieces; Docker drops many by default), **seccomp** (filters allowed system calls), and AppArmor or SELinux profiles.
- Since the kernel is shared, the container is not a security boundary as strong as a VM.
- cgroups v1 and v2 exist. Newer distributions use v2 by default. Older JVMs may not detect v2 limits correctly (see Q35).

```bash
docker run \
  -d \
  --name demo \
  --memory 256m \
  --cpus 0.5 \
  nginx
# Starts a container with cgroup limits

docker inspect \
  --format '{{.State.Pid}}' \
  demo
# Prints the PID of the container's main process on the HOST, for example 12345

ps aux | grep nginx
# On the host, the same nginx process is visible: a container is an ordinary host process
```

**Follow-up questions:**

- **Is a container a security boundary?** Partly. A kernel exploit or a misconfiguration (for example `--privileged`, mounting `docker.sock`) can allow escape. Use non-root users, drop capabilities, and use user namespaces or rootless mode for stronger isolation.
- **What does `--privileged` do?** Gives the container nearly all capabilities and access to host devices. Avoid it.
- **Why is mounting `/var/run/docker.sock` dangerous?** Whoever controls that socket controls the Docker daemon, which is effectively root on the host.

---

---

## 2. Dockerfile and Builds

### Q10. [Basic] What are image layers, and how does Docker layer caching work?

**Short answer:** Each Dockerfile instruction that changes the filesystem creates a read-only layer. Docker reuses a cached layer if the instruction and all earlier layers are unchanged. When one layer changes, that layer and every layer after it are rebuilt.

**Explanation:**

- A **layer** is a set of file changes (added, modified or deleted files) stacked on the previous layer.
- Layers are shared. Two images built from the same base store the base layers once on disk.
- Cache rules: for `RUN`, Docker compares the instruction text. For `COPY` and `ADD`, it compares the checksum of the copied files.
- Cache invalidation only goes forward. A change in step 5 never invalidates steps 1 to 4.
- Rule: put things that change rarely first (base image, dependencies) and things that change often last (source code).

Bad order (any code change re-downloads all dependencies):

```dockerfile
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY . .
RUN ./mvnw package -DskipTests
```

Good order (dependencies are cached until `pom.xml` changes):

```dockerfile
FROM eclipse-temurin:17-jdk
WORKDIR /app

# 1. Copy only the build files. Changes rarely.
COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
# 2. Download dependencies. Cached until pom.xml changes.
RUN ./mvnw dependency:go-offline

# 3. Copy source. Changes on every commit.
COPY src src
RUN ./mvnw package -DskipTests
```

```text
Step                         Code change only      pom.xml change
FROM                         cached                cached
COPY pom.xml                 cached                REBUILT
RUN dependency:go-offline    cached                REBUILT
COPY src                     REBUILT               REBUILT
RUN package                  REBUILT               REBUILT
```

```bash
docker history \
  myapp:1.0
# Lists each layer of the image with the instruction that made it and its size
```

**Follow-up questions:**

- **Why is a file deleted in a later `RUN` still counted in the image size?** Layers are immutable. A delete only adds a "whiteout" marker in a new layer. The old layer still holds the file. Delete in the same `RUN` command that created the file.
- **How do you force a build without cache?** `docker build --no-cache .`
- **Does `RUN apt-get update` get cached forever?** Yes, until its text changes. This can leave stale package lists. Combine `apt-get update && apt-get install -y ...` in one `RUN`.
- **Does a changing timestamp or `COPY . .` break cache?** `COPY . .` breaks cache whenever any file in the context changes (including logs or `target/`). Use `.dockerignore` (Q15) to keep unrelated files out.
- **What is BuildKit?** The newer build engine, default since Docker Engine 23.0 (version-specific). It builds independent stages in parallel and supports cache mounts and build secrets.

---

### Q11. [Basic] What do the main Dockerfile instructions do (FROM, WORKDIR, COPY, RUN, USER, EXPOSE, CMD)?

**Short answer:** A Dockerfile is a text file of build steps. `FROM` picks the base image, `WORKDIR` sets the folder, `COPY` adds files, `RUN` executes commands at build time, `USER` sets the user, `EXPOSE` documents a port, and `CMD`/`ENTRYPOINT` define what runs when a container starts.

| Instruction | What it does |
|---|---|
| `FROM` | Sets the base image for the build. Must be the first instruction (except `ARG`). Each `FROM` starts a new stage. |
| `WORKDIR` | Sets the current folder for later instructions and for the running container. Creates it if missing. Prefer it over `RUN cd`. |
| `COPY` | Copies files from the build context into the image. |
| `RUN` | Runs a command at build time. Its result is saved as a new layer. |
| `ENV` | Sets an environment variable that stays in the image and in running containers. |
| `ARG` | Sets a variable that exists only during the build. |
| `USER` | Sets the user (and group) for later `RUN` instructions and for the container process. |
| `EXPOSE` | Documents the port the app listens on. Does not publish it. |
| `HEALTHCHECK` | Defines a command Docker runs to test whether the app is healthy. |
| `CMD` | Default command or default arguments when the container starts. |
| `ENTRYPOINT` | The fixed executable for the container. |

```dockerfile
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY target/app.jar app.jar
RUN addgroup -S spring && adduser -S spring -G spring
USER spring
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

- **Shell form vs exec form:** `RUN npm install` is shell form (runs through `/bin/sh -c`). `CMD ["java","-jar","app.jar"]` is exec form (JSON array, no shell). Exec form is preferred for `CMD` and `ENTRYPOINT` because the app becomes process 1 and receives stop signals (see Q21).
- **Build context:** the folder sent to the Docker daemon when you run `docker build`. `COPY` can only read files inside it.

**Follow-up questions:**

- **Why use `WORKDIR` instead of `RUN cd /app`?** `cd` in a `RUN` only lasts for that one instruction. `WORKDIR` persists for all later instructions and the container.
- **How many layers does a Dockerfile create?** Every `RUN`, `COPY` and `ADD` creates a layer with content. Other instructions such as `ENV`, `WORKDIR` and `CMD` only add metadata.
- **Is `FROM scratch` valid?** Yes. `scratch` is an empty image. It is used for static binaries (for example Go).

---

### Q12. [Basic] What is the difference between COPY and ADD?

**Short answer:** `COPY` only copies local files. `ADD` also extracts local tar archives and can download from a URL. Use `COPY` unless you need those extra features.

**Explanation:**

- `ADD` has behavior that is easy to miss. It auto-extracts recognized local archives (`.tar`, `.tar.gz`) into the destination.
- `ADD <url>` downloads a file at build time. The downloaded file is not extracted, and it makes caching unpredictable. Using `curl` or `wget` in a `RUN` is clearer, and you can delete the file in the same step.
- `COPY` does what its name says, so Dockerfiles are easier to read and safer.

```dockerfile
# Plain copy: preferred
COPY target/app.jar /app/app.jar

# ADD extracts the archive automatically: valid use of ADD
ADD vendor-libs.tar.gz /opt/libs/

# ADD with a URL: avoid. Use RUN curl instead.
ADD https://example.com/tool.zip /tmp/tool.zip
```

**Follow-up questions:**

- **What does `COPY --from=builder` do?** It copies files from an earlier build stage (see Q16) or another image, instead of from the build context.
- **How do you set file owner while copying?** `COPY --chown=spring:spring target/app.jar app.jar`. This avoids an extra `RUN chown`, which would add a layer with duplicated data.
- **Can `COPY` copy files from outside the build context?** No. Paths like `../config` fail. Change the context (`docker build -f docker/Dockerfile .` from the repo root).

---

### Q13. [Basic] What is the difference between CMD and ENTRYPOINT?

**Short answer:** `ENTRYPOINT` is the fixed program the container runs. `CMD` gives default arguments (or a default command if there is no `ENTRYPOINT`). Anything written after the image name in `docker run` replaces `CMD`, but not `ENTRYPOINT`.

**Explanation:**

- Both only run when a container starts, not at build time (`RUN` is the build-time one).
- Only the last `CMD` and the last `ENTRYPOINT` in a Dockerfile count.
- To replace `ENTRYPOINT` you must use the `--entrypoint` flag.
- Always use exec form (JSON array).

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
CMD ["--server.port=8080"]
```

```bash
docker run \
  myapp
# Runs: java -jar app.jar --server.port=8080

docker run \
  myapp \
  --server.port=9090
# Runs: java -jar app.jar --server.port=9090   (CMD replaced)

docker run \
  --entrypoint sh \
  -it \
  myapp
# Opens a shell instead of starting the app (ENTRYPOINT replaced)
```

| Dockerfile | `docker run image` runs | `docker run image X` runs |
|---|---|---|
| `CMD ["a","b"]` | `a b` | `X` |
| `ENTRYPOINT ["a"]` | `a` | `a X` |
| `ENTRYPOINT ["a"]` + `CMD ["b"]` | `a b` | `a X` |

**Follow-up questions:**

- **Shell form vs exec form for these?** Shell form (`CMD java -jar app.jar`) runs as `/bin/sh -c "..."`. The shell becomes process 1 and may not pass `SIGTERM` to Java. Exec form avoids this.
- **When do you use `ENTRYPOINT` as a script?** When you need setup before the app starts (for example, generating config). End the script with `exec "$@"` so the app replaces the shell and gets signals.
- **What does `command:` in docker-compose do?** It overrides the image's `CMD`. `entrypoint:` overrides `ENTRYPOINT`.

---

### Q14. [Basic] What is the difference between ENV and ARG?

**Short answer:** `ARG` is a variable that exists only while building the image. `ENV` is saved in the image and is available to the running container.

**Explanation:**

- `ARG` is set with `--build-arg` at build time. It is not available in the running container.
- `ENV` can be overridden at run time with `-e`.
- Neither is safe for secrets. `ENV` values are visible in `docker inspect`. `ARG` values appear in `docker history` and can be read from the image metadata. See Q36 for build secrets.
- An `ARG` placed before `FROM` can be used in the `FROM` line, but must be redeclared inside the stage to use it there.

```dockerfile
ARG JAVA_VERSION=17
FROM eclipse-temurin:${JAVA_VERSION}-jre-alpine

ARG APP_VERSION=dev
ENV SPRING_PROFILES_ACTIVE=prod
ENV APP_VERSION=${APP_VERSION}
```

```bash
docker build \
  --build-arg JAVA_VERSION=21 \
  --build-arg APP_VERSION=1.4.2 \
  -t myapp:1.4.2 \
  .
# Builds with Java 21 and writes APP_VERSION into the image as an ENV

docker run \
  -e SPRING_PROFILES_ACTIVE=dev \
  myapp:1.4.2
# Overrides the ENV default at run time
```

| Item | Available at build | Available in running container | Override with |
|---|---|---|---|
| `ARG` | Yes | No | `--build-arg` |
| `ENV` | Yes (after it is set) | Yes | `-e`, `--env-file`, compose `environment:` |

**Follow-up questions:**

- **How does Spring Boot read `ENV` values?** Spring's relaxed binding maps `SPRING_DATASOURCE_URL` to `spring.datasource.url`. So you can configure the app with env vars without rebuilding.
- **Why should one image be used for dev, test and prod?** The same tested image is promoted between environments. Only runtime config (env vars, mounted config files) changes.

---

### Q15. [Basic] What is `.dockerignore` and why is it important?

**Short answer:** `.dockerignore` lists files and folders that are not sent to the Docker daemon in the build context. It makes builds faster, keeps images smaller, and stops secrets or junk from ending up in the image.

**Explanation:**

- On `docker build .`, the whole folder (the **build context**) is sent to the daemon first. Large folders like `.git`, `target/` or `node_modules` slow that down.
- Ignored files cannot be copied by `COPY . .`.
- It also improves layer caching. Without it, a change in a log file or `target/` invalidates `COPY . .`.
- It prevents accidental inclusion of `.env` files and keys.

```text
# .dockerignore
.git
.gitignore
target/
build/
*.log
.env
.env.*
.idea/
*.md
Dockerfile
docker-compose*.yml
```

**Follow-up questions:**

- **Same syntax as `.gitignore`?** Very similar (patterns, `!` for exceptions), but the matching rules are not identical. Patterns are relative to the context root.
- **If I ignore `target/`, how do I `COPY target/app.jar`?** You cannot. Either build inside the Dockerfile (multi-stage, Q16) or do not ignore that path. Multi-stage builds avoid this problem.
- **Can I see how big the context is?** Yes, the build output shows "transferring context: X MB".

---

### Q16. [Intermediate] What are multi-stage builds and why use them?

**Short answer:** A multi-stage build has several `FROM` lines in one Dockerfile. You build in a heavy stage (JDK, Maven or Gradle) and copy only the result (the jar) into a small final stage (JRE). The final image has no compiler, no source code and no build tools.

**Explanation:**

- Each `FROM` starts a new stage with a clean filesystem. `COPY --from=<stage>` takes files from an earlier stage.
- Only the last stage (or the one selected with `--target`) becomes the image. Earlier stages are discarded but stay in the build cache.
- Benefits: smaller image, smaller attack surface, and the build is reproducible without needing Maven or Java installed on the CI server.

```dockerfile
# ---------- Stage 1: build ----------
FROM eclipse-temurin:17-jdk-alpine AS builder
WORKDIR /app
COPY gradlew .
COPY gradle gradle/
COPY build.gradle settings.gradle ./
RUN chmod +x gradlew && ./gradlew dependencies --no-daemon
COPY src src/
RUN ./gradlew bootJar --no-daemon

# ---------- Stage 2: runtime ----------
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
RUN addgroup -S spring && adduser -S spring -G spring
COPY --from=builder --chown=spring:spring /app/build/libs/*.jar app.jar
USER spring
EXPOSE 8090
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```bash
docker build \
  --target builder \
  -t myapp:builder \
  .
# Builds only up to the "builder" stage (useful to run tests or debug the build)
```

```text
Stage 1 (builder): JDK + Gradle + source + jar   ~ 800 MB   -> discarded
Stage 2 (runtime): JRE + app.jar                 ~ 200 MB   -> final image
                         ^
                         |  COPY --from=builder  (only the jar)
```

**Follow-up questions:**

- **How do you run tests during the build?** Add a stage: `FROM builder AS test` with `RUN ./gradlew test`, and build it with `--target test` in CI. Or run `test` in the builder stage before `bootJar`.
- **Can you copy from an external image?** Yes: `COPY --from=nginx:1.27 /etc/nginx/nginx.conf /tmp/`.
- **Does the final image contain the builder's layers?** No. Only what you copy.
- **What does BuildKit add?** Independent stages are built in parallel, and unused stages are skipped (version-specific: BuildKit is the default builder from Docker Engine 23.0).

---

### Q17. [Intermediate] How do you reduce Docker image size?

**Short answer:** Use a small base image, use multi-stage builds, combine and clean up in the same `RUN`, add a `.dockerignore`, install only what you need, and for Java use a JRE (or a custom `jlink` runtime) instead of a full JDK.

**Explanation:**

| Technique | What it does |
|---|---|
| Small base image | `alpine`, `-slim`, `distroless`, or `-jre` tags are smaller than full OS or JDK images. |
| Multi-stage build | Leaves compilers and source code out of the final image. |
| Clean in the same layer | Deleting a file in a later layer does not shrink the image (layers are immutable). |
| `--no-install-recommends` and cache cleanup | Avoids extra packages on Debian or Ubuntu. |
| `.dockerignore` | Keeps `.git`, `target/` and docs out of the context and layers. |
| JRE or `jlink` | A custom runtime with only the Java modules the app needs. |
| Fewer, larger dependencies | Remove unused dependencies from `pom.xml`. |

```dockerfile
# Debian-based: install and clean in ONE layer
RUN apt-get update \
 && apt-get install -y --no-install-recommends curl \
 && rm -rf /var/lib/apt/lists/*

# Alpine: --no-cache avoids storing the package index
RUN apk add --no-cache curl
```

```bash
docker images \
  myapp
# Compare sizes before and after the change

docker history \
  --no-trunc \
  myapp:1.0
# Shows which layers are large
```

**Follow-up questions:**

- **Alpine or Debian-slim for Java?** Alpine uses `musl` libc instead of `glibc`. Some native libraries and some performance-sensitive code can behave differently. Debian-slim (or Ubuntu Chiseled, Distroless) is safer when you use native code. Test either choice.
- **What is distroless?** Images with only the app runtime and no shell or package manager. Very small and safer, but harder to debug.
- **Is a smaller image always better?** It pulls faster and has fewer vulnerabilities. But debuggability and compatibility matter too.

---

### Q18. [Intermediate] What is HEALTHCHECK in a Dockerfile and how is it different from a Kubernetes probe?

**Short answer:** `HEALTHCHECK` defines a command Docker runs inside the container on a schedule. A zero exit code means healthy. Docker marks the container `healthy` or `unhealthy`, but plain Docker does not restart it. Kubernetes ignores the Dockerfile `HEALTHCHECK` and uses its own probes.

```dockerfile
FROM eclipse-temurin:17-jre-alpine
RUN apk add --no-cache curl
COPY app.jar /app/app.jar
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD curl -f http://localhost:8080/actuator/health || exit 1
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
```

```bash
docker ps
# STATUS column shows: Up 2 minutes (healthy)

docker inspect \
  --format '{{json .State.Health}}' \
  backend
# Shows the status and the last check results
```

**Explanation:**

- A weak check only tests that a process exists. A good check calls a real endpoint such as Spring Boot Actuator `/actuator/health`.
- A check inside the container needs a tool like `curl` or `wget`. Slim images may not have it. Options: install it, use a small Java or shell check, or define the check in Compose or Kubernetes instead.
- A compose `healthcheck:` overrides the one in the Dockerfile.
- Do not make the health check depend on external systems too strictly. If the database is down and every instance reports unhealthy, an orchestrator may restart all instances at once.

**Follow-up questions:**

- **Liveness vs readiness?** Liveness asks "is the app stuck and needs a restart?". Readiness asks "can it receive traffic now?". Docker's `HEALTHCHECK` is one signal. Kubernetes has separate probes (Q41). Spring Boot Actuator has `/actuator/health/liveness` and `/actuator/health/readiness` (Spring Boot 2.3+).
- **How to disable an inherited healthcheck?** `HEALTHCHECK NONE` in the Dockerfile, or `healthcheck: { disable: true }` in Compose.

---

### Q19. [Intermediate] How do you containerize a Spring Boot application? (production Dockerfile)

**Short answer:** Use a multi-stage build: build the jar with Maven or Gradle, then copy it to a JRE image, run as a non-root user, set sensible JVM options, and use exec-form `ENTRYPOINT` so the JVM receives stop signals.

```dockerfile
# ---------- build ----------
FROM eclipse-temurin:17-jdk AS builder
WORKDIR /workspace
COPY mvnw .
COPY .mvn .mvn
COPY pom.xml .
RUN ./mvnw -q dependency:go-offline
COPY src src
RUN ./mvnw -q package -DskipTests

# ---------- runtime ----------
FROM eclipse-temurin:17-jre
WORKDIR /app
RUN groupadd --system spring && useradd --system --gid spring spring
COPY --from=builder --chown=spring:spring /workspace/target/*.jar app.jar
USER spring
EXPOSE 8080
ENV JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=75.0 -XX:+ExitOnOutOfMemoryError"
HEALTHCHECK --interval=30s --timeout=3s --start-period=60s \
  CMD wget -qO- http://localhost:8080/actuator/health || exit 1
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**Explanation:**

- `JAVA_TOOL_OPTIONS` is picked up by every JVM started in the container and can be overridden at run time. It prints a line "Picked up JAVA_TOOL_OPTIONS" on start (this is normal).
- `-XX:+ExitOnOutOfMemoryError` makes the JVM exit on heap OOM so the orchestrator restarts it, rather than leaving a half-broken process.
- Choose the base image deliberately (Q17). The `wget` in the healthcheck exists in Debian-based images but may not exist in all slim ones.
- Alternatives to a hand-written Dockerfile:
  - Spring Boot buildpacks: `./mvnw spring-boot:build-image` (needs a Docker daemon; builds a layered, optimized image without a Dockerfile).
  - Jib (Maven/Gradle plugin): builds an image without Docker installed.

```bash
docker build \
  -t myapp:1.0 \
  .
# Builds the image

docker run \
  -d \
  --name myapp \
  -p 8080:8080 \
  --memory 1g \
  -e SPRING_PROFILES_ACTIVE=prod \
  myapp:1.0
# Runs it with a 1 GB limit; the JVM sizes its heap from that limit
```

**Follow-up questions:**

- **Why not copy `target/*.jar` from the host in the Dockerfile?** The build then depends on the developer machine. Building inside Docker (multi-stage) is reproducible.
- **How do you make rebuilds fast?** Layer order (Q10), BuildKit cache mounts (`RUN --mount=type=cache,target=/root/.m2 ...`), and layered jars (Q20).
- **How do you pass the DB URL?** As an environment variable such as `SPRING_DATASOURCE_URL`, never baked into the image.

---

### Q20. [Advanced] What are layered jars in Spring Boot, and why do they improve Docker builds?

**Short answer:** A Spring Boot layered jar splits the application into layers (dependencies, Spring Boot loader, snapshot dependencies, application code). In the Dockerfile each part is a separate `COPY`, so when only your code changes, only the small application-code layer is rebuilt and pushed.

**Explanation:**

- A normal fat jar is a single 50 to 200 MB file. Any code change creates a new jar, so Docker rebuilds that whole layer and each push and pull transfers the full file.
- Dependencies change rarely. Application code changes on each commit. Splitting them uses layer caching well (Q10).
- Layers are enabled by default for the Spring Boot Maven and Gradle plugins from Spring Boot 2.4 (earlier: opt in from 2.3).
- Extract tool (version-specific):
  - Spring Boot 2.3 to 3.2: `java -Djarmode=layertools -jar app.jar extract`.
  - Spring Boot 3.3 and later: `java -Djarmode=tools -jar app.jar extract --layers --launcher` (the old `layertools` mode is deprecated). Check the Spring Boot documentation for your version.

```dockerfile
# ---------- extract layers ----------
FROM eclipse-temurin:17-jre AS builder
WORKDIR /layers
COPY target/app.jar app.jar
RUN java -Djarmode=layertools -jar app.jar extract

# ---------- final image ----------
FROM eclipse-temurin:17-jre
WORKDIR /app
RUN groupadd --system spring && useradd --system --gid spring spring
COPY --from=builder --chown=spring:spring /layers/dependencies/ ./
COPY --from=builder --chown=spring:spring /layers/spring-boot-loader/ ./
COPY --from=builder --chown=spring:spring /layers/snapshot-dependencies/ ./
COPY --from=builder --chown=spring:spring /layers/application/ ./
USER spring
# Spring Boot 2.x and 3.0-3.1 launcher class. In Spring Boot 3.2+ it is
# org.springframework.boot.loader.launch.JarLauncher.
ENTRYPOINT ["java", "org.springframework.boot.loader.JarLauncher"]
```

```text
Layer (order: least to most frequently changed)
1. dependencies             (third-party jars)       rarely changes
2. spring-boot-loader       (launcher classes)       rarely changes
3. snapshot-dependencies    (SNAPSHOT jars)          sometimes
4. application              (your classes)           every commit
```

**Follow-up questions:**

- **How much do you save?** The code layer is often under a few MB, so rebuild, push and pull become much faster than moving a full fat jar.
- **Other ways to speed up builds?** BuildKit cache mounts for `~/.m2` or `~/.gradle`, Jib, and Spring Boot buildpacks, which also layer the image.
- **Does this help startup time?** Slightly (unpacked classes can start faster). For bigger gains look at AppCDS or GraalVM native images (different trade-offs).

---

### Q21. [Advanced] What is PID 1 in a container, and how do you get graceful shutdown?

**Short answer:** The first process in a container is PID 1, and it receives the stop signal (`SIGTERM`) from `docker stop`. If PID 1 is a shell that does not pass the signal on, the app is never told to stop, and Docker kills it after the timeout. Use exec-form `ENTRYPOINT` or `exec` in scripts, and add an init process if needed.

**Explanation:**

- Shell form (`ENTRYPOINT java -jar app.jar`) runs `/bin/sh -c "java -jar app.jar"`. The shell is PID 1 and Java is its child. The shell usually does not forward `SIGTERM`.
- Exec form (`["java","-jar","app.jar"]`) makes Java PID 1, so it receives the signal.
- Spring Boot graceful shutdown: set `server.shutdown=graceful` and `spring.lifecycle.timeout-per-shutdown-phase=30s` (Spring Boot 2.3+). The app finishes in-flight requests before exit.
- Kubernetes sends `SIGTERM`, waits `terminationGracePeriodSeconds` (default 30 s), then `SIGKILL`. Set the grace period longer than the app's shutdown timeout.
- PID 1 also has special duties in Linux: it should reap zombie child processes and has default signal handling that differs from other processes. If an app spawns many child processes, use a tiny init: `docker run --init` or `tini`.

```dockerfile
# Bad: shell form. "docker stop" waits 10 s, then kills.
ENTRYPOINT java -jar app.jar

# Good: exec form. Java is PID 1 and gets SIGTERM.
ENTRYPOINT ["java", "-jar", "app.jar"]
```

```bash
#!/bin/sh
# entrypoint.sh: prepare config, then replace the shell with the app
set -e
envsubst < /app/config.tpl > /app/config.yml
exec java -jar /app/app.jar "$@"
# "exec" replaces the shell process, so Java becomes PID 1
```

```yaml
# application.yml
server:
  shutdown: graceful
spring:
  lifecycle:
    timeout-per-shutdown-phase: 30s
```

**Follow-up questions:**

- **How do you check which process is PID 1?** `docker exec backend ps -o pid,cmd 1` or `docker top backend`.
- **What does `docker run --init` do?** Starts a tiny init program (tini) as PID 1, which forwards signals and reaps zombies.
- **How does this relate to zero-downtime deploys?** Combined with a readiness probe that fails during shutdown, load balancers stop sending traffic before the app stops.

---

---

## 3. Networking, Volumes and Compose

### Q22. [Basic] What does EXPOSE do, and how does port publishing (`-p`) work?

**Short answer:** `EXPOSE` is documentation only. `-p HOST:CONTAINER` is what actually makes a container port reachable from the host, by forwarding traffic from a host port to the container port.

**Explanation:**

- A container has its own network. Nothing outside can reach its ports until you publish them.
- `-p 8080:5000` means: host port 8080 forwards to container port 5000. The left side is what you type in the browser. The right side must be the port the app really listens on.
- If `server.port=1010` but you publish `-p 8080:5000`, nothing listens on 5000 and the request fails with "connection refused".
- A wrong `EXPOSE` value changes nothing when you use `-p`.
- `-P` (capital) publishes all `EXPOSE`d ports to random host ports. This is the one case where `EXPOSE` matters.
- By default `-p 8080:80` listens on all host interfaces (`0.0.0.0`). Use `-p 127.0.0.1:8080:80` to allow only local access.

```bash
docker run \
  -d \
  -p 8080:5000 \
  in28min/hello-world-java:0.0.1.RELEASE
# Host port 8080 forwards to container port 5000. Open http://localhost:8080

docker port \
  <container>
# Shows the published port mappings of that container

docker run \
  -d \
  -p 127.0.0.1:5432:5432 \
  postgres:16
# Publishes the DB port to the local machine only, not to the network
```

```yaml
services:
  backend:
    image: myapp:1.0
    ports:
      - "8090:8090"      # published to the host
  mysql:
    image: mysql:8.0
    expose:
      - "3306"           # reachable only by other containers on the network
```

**Follow-up questions:**

- **Do containers on the same network need published ports to talk to each other?** No. They use the container port directly (`http://backend:8090`). Published ports are only for traffic coming from outside Docker.
- **Why do published ports bypass the host firewall (UFW) on Linux?** (Linux-specific) Docker adds its own iptables rules, which can skip the rules set by UFW. Bind to `127.0.0.1` or use a reverse proxy to avoid exposing a database by accident.
- **Two containers want the same host port. What happens?** The second one fails to start with "port is already allocated". Use different host ports; container ports can be the same.

---

### Q23. [Basic] What is the difference between volumes and bind mounts? How do you persist data?

**Short answer:** A volume is storage managed by Docker (stored under Docker's own directory). A bind mount maps a specific host path into the container. Both survive container removal. Data in the container's writable layer does not.

**Explanation:**

- By default, files written inside a container are in its writable layer and are deleted with `docker rm`.
- **Named volume:** Docker creates and manages it. Best for database data. It is portable, easy to back up with Docker tools, and works the same on all operating systems.
- **Bind mount:** you pick the host path. Best for local development (source code with live reload) and config files you edit on the host.
- **tmpfs mount:** kept in host memory only. Used for temporary or sensitive data that must not touch disk.
- Bind mounts can have file permission problems because the container user ID must match the host file owner.

```bash
docker volume create \
  mysql_data
# Creates a named volume

docker run \
  -d \
  --name db \
  -e MYSQL_ROOT_PASSWORD=secret \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0
# Database files are stored in the volume. Removing the container keeps the data.

docker run \
  -d \
  --name backend \
  -v "$(pwd)/uploads:/app/uploads" \
  myapp:1.0
# Bind mount: the host folder ./uploads appears at /app/uploads

docker run \
  -d \
  --tmpfs /app/tmp \
  myapp:1.0
# tmpfs mount: in memory, gone when the container stops
```

| Type | Who controls the location | Typical use |
|---|---|---|
| Named volume | Docker | Database data, data that must persist |
| Bind mount | You (host path) | Dev source code, config files, uploads you want to see |
| tmpfs | Memory | Temporary or sensitive data |

**Follow-up questions:**

- **What happens to a volume on `docker rm`?** It stays. Use `docker rm -v` for anonymous volumes or `docker volume rm` / `docker volume prune` (permanent data loss).
- **What does `docker compose down -v` do?** Removes containers, networks, and the named volumes declared in the file. This wipes database data.
- **What if a bind mount hides files from the image?** A mount over a folder hides what the image had at that path. A new empty named volume, in contrast, is first filled with the image's content at that path.
- **How do volumes work in Kubernetes?** Through `PersistentVolumeClaim` objects backed by storage such as EBS on AWS. The container does not manage the storage itself.
- **Can multiple containers share one volume?** Yes, but the app must handle concurrent writes. Databases must not share one data directory between two running servers.

---

### Q24. [Basic] What are Docker networking modes (bridge, host, none)?

**Short answer:** `bridge` (default) gives each container a private IP on a virtual network on the host and needs port publishing for outside access. `host` makes the container use the host's network directly. `none` gives no network.

| Driver | What it does | When to use |
|---|---|---|
| `bridge` | Private virtual network on one host. Containers get their own IP. Outside traffic needs `-p`. | Default for most apps |
| `host` | No network isolation. The container uses the host's network. `-p` is ignored. | Very high network performance, or apps needing many ports (Linux only for full behavior) |
| `none` | Only a loopback interface. No outside access. | Isolated batch jobs |
| `overlay` | Network across multiple hosts (Swarm). | Multi-host clusters |

```text
            Host
+---------------------------------------+
|  bridge network (docker0 / custom)    |
|  +----------+      +----------+       |
|  | backend  |<---->|  mysql   |       |
|  | 172.18.0.2|     | 172.18.0.3|      |
|  +----------+      +----------+       |
+----------|----------------------------+
           | -p 8080:8080 (NAT rule)
        Host port 8080  <---- outside traffic
```

```bash
docker run \
  -d \
  --network host \
  nginx
# Container uses the host network. Nginx is on the host's port 80 directly.

docker network ls
# Lists networks: bridge, host, none, and your own

docker network inspect \
  bridge
# Shows which containers are attached and their IP addresses
```

**Follow-up questions:**

- **What is `localhost` inside a container?** The container itself, not the host and not other containers. A Spring Boot app in a container cannot use `localhost:3306` to reach MySQL in another container. Use the service name (`mysql:3306`).
- **How does a container reach a service running on the host machine?** On Docker Desktop use `host.docker.internal`. On Linux add `--add-host=host.docker.internal:host-gateway`.
- **Why avoid `host` mode in production?** No isolation and port conflicts with the host.

---

### Q25. [Intermediate] How does container-to-container networking and DNS work? Why use user-defined networks?

**Short answer:** On a user-defined bridge network (including the one Compose creates), Docker runs an embedded DNS server that resolves container and service names to IPs. The default `bridge` network does not provide name resolution.

**Explanation:**

- Containers on the same user-defined network can call each other by name, for example `http://backend:8090` or `jdbc:mysql://mysql:3306/app`.
- The name is resolved by Docker's embedded DNS (at `127.0.0.11` inside the container). It is not a real DNS name on the internet.
- The default `bridge` network only supports IP-based access (name access needs the legacy `--link`, which is deprecated).
- User-defined networks isolate groups of containers. A container on `frontend-net` cannot reach one that is only on `db-net`.
- Compose creates a project network by default and attaches all services to it.
- Container IPs change when containers are recreated, so never hardcode them.

```bash
docker network create \
  app-net
# Creates a user-defined bridge network

docker run \
  -d \
  --name mysql \
  --network app-net \
  -e MYSQL_ROOT_PASSWORD=secret \
  mysql:8.0
# MySQL is reachable as "mysql" on app-net

docker run \
  --rm \
  --network app-net \
  busybox \
  nslookup mysql
# Prints the IP of the mysql container (resolved by Docker DNS)
```

```yaml
services:
  backend:
    image: myapp:1.0
    networks: [public-net, db-net]
  mysql:
    image: mysql:8.0
    networks: [db-net]       # not reachable from public-net
  proxy:
    image: nginx:1.27
    networks: [public-net]
    ports: ["80:80"]

networks:
  public-net:
  db-net:
```

**Follow-up questions:**

- **Why can a browser not call `http://backend:8090`?** The browser runs outside Docker and cannot resolve that name. The browser needs a host-published port or domain. Server-side code in a container can use the service name.
- **Two containers have the same alias on one network. What happens?** DNS returns multiple IPs, which gives simple round-robin. Compose uses this when you scale a service.
- **How do containers on different hosts talk?** Overlay networks (Swarm), or Kubernetes networking and Services.

---

### Q26. [Intermediate] What is Docker Compose and how do you write a compose file for a Spring Boot app with a database?

**Short answer:** Docker Compose defines and runs several containers (services) together from one YAML file. One command (`docker compose up`) creates the networks, volumes and containers.

**Explanation:**

- **Service:** one container definition (image or build, ports, env, volumes).
- Compose puts all services on a shared network, so they reach each other by service name.
- `docker compose` (v2, a Docker CLI plugin) replaced `docker-compose` (v1, a separate Python tool, end-of-life). The top-level `version:` key is obsolete in Compose v2 and is ignored (version-specific).

```yaml
services:
  mysql:
    image: mysql:8.0
    env_file:
      - ./.env                        # DB passwords, kept out of Git
    volumes:
      - mysql_data:/var/lib/mysql     # named volume: data survives container removal
    networks:
      - app-net
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 10

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "8090:8090"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/app   # "mysql" is the service name
      SPRING_PROFILES_ACTIVE: dev
    depends_on:
      mysql:
        condition: service_healthy    # wait for the healthcheck to pass
    restart: unless-stopped
    networks:
      - app-net

volumes:
  mysql_data:

networks:
  app-net:
    driver: bridge
```

```bash
docker compose up \
  -d \
  --build
# Builds images if needed and starts all services in the background

docker compose ps
# Shows status and health of each service

docker compose logs \
  -f \
  backend
# Streams logs of the backend service

docker compose down
# Stops and removes containers and the network. Named volumes are kept.

docker compose down \
  -v
# Also removes the named volumes (deletes database data)
```

**Follow-up questions:**

- **`docker compose up` vs `start` vs `restart`?** `up` creates (if needed) and starts. `start` starts existing stopped containers. `restart` restarts running containers. `up` recreates a container if its config or image changed.
- **How do you scale a service?** `docker compose up -d --scale backend=3`. You cannot have a fixed `container_name` or a fixed host port for it, or the copies will clash.
- **How do you see the final merged config?** `docker compose config`.
- **Is Compose suitable for production?** For one host or small setups, yes. For many hosts, auto-scaling and self-healing, use Kubernetes (Q33).

---

### Q27. [Intermediate] What does `depends_on` do, and why do you need healthchecks?

**Short answer:** `depends_on` controls start order. By default it waits only until the dependency container has started, not until the application inside is ready. Add a `healthcheck` and `condition: service_healthy` to wait for readiness.

**Explanation:**

- MySQL needs several seconds after the container starts before it accepts connections. If the backend starts too early, it fails to connect and may crash.
- Conditions: `service_started` (default), `service_healthy` (healthcheck passes), `service_completed_successfully` (a one-time job finished, for example a migration).
- `depends_on` does not replace retry logic in the app. In production the database can restart at any time. Configure connection retries in the app (for example Spring Retry or the Hikari pool settings).
- Compose only gates startup. It does not restart a service when its dependency later becomes unhealthy.

```yaml
services:
  mysql:
    image: mysql:8.0
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 10
      start_period: 30s      # failures in this window are not counted

  migrate:
    image: myapp-migrations:1.0
    depends_on:
      mysql:
        condition: service_healthy

  backend:
    image: myapp:1.0
    depends_on:
      mysql:
        condition: service_healthy
      migrate:
        condition: service_completed_successfully
```

**Follow-up questions:**

- **What are the healthcheck fields?** `test` (command; exit 0 = healthy), `interval` (time between checks), `timeout` (max time for one check), `retries` (failures before "unhealthy"), `start_period` (grace time at startup).
- **What states can a container health have?** `starting`, `healthy`, `unhealthy`.
- **Does Docker restart an unhealthy container?** Not in plain Docker or Compose. It only marks it. Swarm replaces unhealthy tasks, and Kubernetes uses liveness probes to restart (Q41).

---

### Q28. [Intermediate] How do you manage environments in Compose (env files, profiles, override files)?

**Short answer:** Use `env_file` and variable substitution for config, `profiles` to start only some services, and extra compose files (`-f`, or `docker-compose.override.yml`) to change settings per environment.

**Explanation:**

- **`env_file:`** loads variables into the container from a file.
- **`.env` file** in the project folder is read by Compose itself for `${VAR}` substitution inside the YAML (this is different from `env_file`).
- **`profiles:`** (Compose v1.28 or later and v2) mark services that start only when their profile is enabled. Services with no profile always start.
- **Override files:** `docker-compose.override.yml` is merged automatically over `docker-compose.yml`. For explicit environments, use `-f base.yml -f prod.yml`.

```yaml
services:
  backend:
    image: myapp:${APP_TAG:-latest}     # substituted from .env or shell; default "latest"
    env_file:
      - ./backend/.env.dev

  mysql:
    image: mysql:8.0

  adminer:
    image: adminer
    ports: ["8081:8080"]
    profiles: ["debug"]        # starts only with --profile debug

  mailhog:
    image: mailhog/mailhog
    profiles: ["dev"]
```

```bash
docker compose up \
  -d
# Starts backend and mysql only (services without profiles)

docker compose \
  --profile debug \
  up \
  -d
# Also starts adminer

docker compose \
  -f docker-compose.yml \
  -f docker-compose.prod.yml \
  up \
  -d
# Base file plus production overrides. Later files win.
```

**Follow-up questions:**

- **Precedence of environment values?** Roughly: `docker compose run -e` > `environment:` in the file > `env_file:` > `ENV` in the Dockerfile. A shell variable does not override a fixed `environment:` value. It is used for `${VAR}` substitution and for `environment:` entries without a value. Check with `docker compose config`.
- **Should `.env` be committed to Git?** No for real secrets. Commit a `.env.example` instead.
- **What does `restart:` do in a compose file?** Sets the restart policy (Q6).

---

### Q29. [Advanced] A port is already in use or a container is not reachable. How do you debug?

**Short answer:** Separate "cannot start because the port is taken" from "started but not reachable". For the first, find what holds the host port and change the mapping. For the second, check that the app listens on `0.0.0.0` on the same port as the right side of `-p`.

**Explanation:**

**Case 1: "port is already allocated" or "address already in use"**

```bash
docker ps \
  --format 'table {{.Names}}\t{{.Ports}}'
# Shows which container already publishes the port

ss -ltnp | grep 8080
# Linux: shows which process listens on host port 8080

netstat -ano | findstr :8080
# Windows: shows the PID that uses port 8080
```

Fix: stop the other process, or change the left side: `-p 8081:8080`.

**Case 2: container is running but the request fails**

| Check | Command or question |
|---|---|
| Is the container really running? | `docker ps`; check `docker logs` |
| Does the port mapping match the app's port? | `docker port backend`; the right side must equal `server.port` |
| Does the app bind to all interfaces? | An app bound to `127.0.0.1` inside the container is not reachable through a published port. Bind to `0.0.0.0` (Spring Boot does this by default). |
| Does it work from inside? | `docker exec backend curl -s localhost:8080/actuator/health` |
| Firewall or security group? | On AWS, check the security group and network ACL for the port. |
| From another container? | Use the service name and the container port, on the same network. |
| Is the app still starting? | Spring Boot can take 30 or more seconds. Check logs for "Started ...Application". |

```bash
docker exec \
  backend \
  curl -s \
  http://localhost:8080/actuator/health
# If this works but the host request fails, the problem is the port mapping or the network
```

**Follow-up questions:**

- **How do you pick any free port?** `-p 8080` (only the container port) makes Docker choose a random host port. `docker port backend` shows it.
- **Why does `localhost` not work between two containers?** Each container has its own loopback address. Use the service or container name on a shared network.
- **Connection works from the host but not from the other machine?** The port might be bound to `127.0.0.1` only (`-p 127.0.0.1:8080:8080`), or a firewall blocks it.

---

### Q30. [Advanced] A container has DNS or network problems (cannot resolve a name or reach another service). How do you debug?

**Short answer:** Test from inside the container: resolve the name, ping or connect to the IP and port, confirm both containers are on the same user-defined network, and check the DNS configuration.

**Explanation:**

```bash
docker network inspect \
  app-net
# Check that both containers are listed under "Containers"

docker exec \
  backend \
  nslookup mysql
# Resolves the service name through Docker DNS. If it fails, they are probably on different networks.

docker exec \
  backend \
  getent hosts mysql
# Alternative when nslookup is missing

docker exec \
  backend \
  nc -zv mysql 3306
# Tests the TCP connection. "open" means the port is reachable.

docker exec \
  backend \
  cat /etc/resolv.conf
# Shows the DNS server. Inside a user-defined network it is 127.0.0.11 (Docker's DNS)

docker run \
  --rm \
  -it \
  --network container:backend \
  nicolaka/netshoot
# Debug container that shares the backend's network namespace with tools like dig, curl, tcpdump
```

Common causes and fixes:

| Problem | Cause | Fix |
|---|---|---|
| Name not found | Containers are on the default `bridge` or on different networks | Put them on the same user-defined network (Compose does this by default) |
| Name resolves, connection refused | App not started yet, wrong port, or app listens on `127.0.0.1` | Check logs, port, binding. Add a healthcheck and `depends_on` (Q27). |
| Cannot reach the internet or external DNS | Corporate DNS or VPN, wrong host DNS | Set `dns` in `daemon.json` or `--dns 8.8.8.8` |
| Works on Linux, fails with a VPN on a laptop | VPN changes routes or MTU | Adjust Docker network MTU or subnet to avoid overlap with VPN ranges |
| Stale IP after redeploy | App cached the DNS result | JVM caches DNS lookups: configure `networkaddress.cache.ttl` (a Java security property) |
| Intermittent failures on a very busy host | Connection tracking or ephemeral ports exhausted | Tune kernel limits, reuse connections (connection pools) |

```json
{
  "dns": ["10.0.0.2", "8.8.8.8"]
}
```

This goes in `/etc/docker/daemon.json` to set DNS servers used by containers.

**Follow-up questions:**

- **Why do ping tests sometimes mislead?** Some images lack `ping`, and ICMP may be blocked while TCP works. Test the actual port (`nc` or `curl`).
- **How does Kubernetes DNS differ?** CoreDNS gives Services names like `myservice.namespace.svc.cluster.local`. Debug with `kubectl exec` and `nslookup`, and check CoreDNS pods.
- **Containers cannot reach each other across two compose projects?** Each project has its own network. Attach both to a shared external network (`networks: shared: external: true`).

---

---

## 4. Production, Security and Kubernetes Basics

### Q31. [Intermediate] How do you limit CPU and memory for a container?

**Short answer:** Use `--memory` and `--cpus` (or `mem_limit`/`cpus` and `deploy.resources.limits` in Compose). Memory is a hard limit: if the container goes over it, the kernel kills the process (OOM kill, exit code 137). CPU is throttled, not killed.

```bash
docker run \
  -d \
  --name backend \
  --memory 1g \
  --memory-swap 1g \
  --cpus 1.5 \
  myapp:1.0
# Max 1 GB RAM (swap disabled because memory-swap equals memory), max 1.5 CPU cores

docker stats \
  --no-stream \
  backend
# Shows current usage against the limit
```

```yaml
services:
  backend:
    image: myapp:1.0
    deploy:
      resources:
        limits:
          cpus: "1.5"
          memory: 1G
        reservations:
          memory: 512M
```

**Explanation:**

- Without limits, one container can use all memory and CPU of the host and starve others.
- Limits are enforced by Linux **cgroups** (Q9).
- `--memory-swap` is memory plus swap. Setting it equal to `--memory` disables swap for the container.
- `--cpu-shares` is a relative weight that only matters under contention. `--cpus` is a hard cap.
- Memory limit includes more than the Java heap: metaspace, thread stacks, direct buffers and JVM overhead (Q35).
- In Compose v2, `deploy.resources` is applied by `docker compose up` (older v1 ignored it outside Swarm unless `--compatibility` was used) (version-specific).
- Kubernetes equivalent: `resources.requests` (used for scheduling) and `resources.limits` (enforced).

**Follow-up questions:**

- **Throttled CPU vs killed memory: why the difference?** CPU time can be shared out later. Memory that is already allocated cannot be taken back, so the kernel must kill a process.
- **How do you know a container was OOM killed?** `docker inspect` shows `OOMKilled: true`, and `dmesg` shows "Out of memory: Killed process" (see Q38).

---

### Q32. [Intermediate] How do you pass configuration and secrets to a container?

**Short answer:** Configuration: environment variables, `--env-file`, or mounted config files. Secrets: do not bake them into the image or commit them. Use runtime injection from a secrets manager or Docker/Kubernetes secrets mounted as files.

**Explanation:**

- Do not put secrets in `ENV`, `ARG` or `COPY` instructions. They stay in image layers and in `docker history` or `docker inspect`, and anyone who can pull the image can read them.
- Environment variables are simple but are visible in `docker inspect`, `/proc/<pid>/environ` and sometimes in crash dumps and logs.
- Better options:
  - Docker Compose `secrets:` (mounted as a file at `/run/secrets/<name>`).
  - Docker Swarm secrets (encrypted in the cluster, mounted as files in memory).
  - Kubernetes Secrets (base64-encoded by default, not encrypted. Enable encryption at rest and use RBAC), or external tools: AWS Secrets Manager, AWS Parameter Store, HashiCorp Vault.
- Spring Boot can read from a directory of files with `spring.config.import=configtree:/run/secrets/` (Spring Boot 2.4+).

```yaml
services:
  backend:
    image: myapp:1.0
    environment:
      SPRING_DATASOURCE_USERNAME: app
      SPRING_CONFIG_IMPORT: configtree:/run/secrets/   # each file becomes a property
    secrets:
      - source: db_password
        target: spring.datasource.password             # file name = property name

secrets:
  db_password:
    file: ./secrets/db_password.txt     # keep this file out of Git
```

```bash
docker run \
  -d \
  --env-file ./app.env \
  myapp:1.0
# Loads KEY=value lines from a file at run time (file must not be in the image or in Git)
```

**Follow-up questions:**

- **Does `.env` or `env_file` make secrets safe?** It keeps them out of the YAML and Git (if gitignored), but they are still plaintext on disk and visible via `docker inspect`.
- **Are Kubernetes Secrets secure?** Not by default. They are base64 (an encoding, not encryption). Enable encryption at rest, restrict access with RBAC, or use an external secrets manager.
- **What if a secret was committed or baked into an image?** Treat it as leaked. Rotate it. Removing it in a later layer does not remove it from earlier layers.

---

### Q33. [Intermediate] Docker Compose vs Docker Swarm vs Kubernetes: what is the difference?

**Short answer:** Compose runs multiple containers on one host. Swarm is Docker's built-in orchestrator for a cluster of hosts and is simple. Kubernetes is the industry-standard orchestrator with many more features and a larger ecosystem, and is more complex.

**Explanation:**

An **orchestrator** is software that runs containers across many machines: it schedules them onto nodes, restarts failed ones, scales them, routes traffic and does rolling updates.

| Feature | Compose | Docker Swarm | Kubernetes |
|---|---|---|---|
| Scope | One host | Cluster | Cluster |
| Setup effort | Very low | Low | High (or use a managed service: AWS EKS) |
| Self-healing | Restart policy only | Yes | Yes |
| Auto-scaling | No | No (manual scale) | Yes (HPA, cluster autoscaler) |
| Rolling updates | No | Yes | Yes, with rollback |
| Load balancing | No | Built-in | Services, Ingress |
| Ecosystem | Small | Small, little new development | Very large (Helm, operators, service mesh) |
| Typical use | Dev, CI, small setups | Small clusters | Production at scale |

**Follow-up questions:**

- **Why is Kubernetes more common than Swarm?** Wide cloud support (EKS, AKS, GKE), larger ecosystem and community, and more features. Swarm is still maintained but sees little adoption.
- **Do you need to change your Docker image for Kubernetes?** No. The same OCI image runs in both. Kubernetes uses containerd or CRI-O as the runtime instead of the full Docker Engine (since Kubernetes 1.24, the built-in "dockershim" was removed), and images built with Docker still work (version-specific).
- **Which AWS services run containers?** ECS (AWS's own orchestrator), EKS (managed Kubernetes), Fargate (serverless compute for ECS and EKS), and App Runner.

---

### Q34. [Intermediate] A container exits immediately after starting. How do you debug it?

**Short answer:** A container stops when its main process (PID 1) exits. Check `docker ps -a` for the exit code, read `docker logs`, then `docker inspect`, and reproduce with an overridden entrypoint to look around.

**Explanation:**

Common causes:

| Symptom | Likely cause |
|---|---|
| Exit code 0 right away | The main command is not a long-running process (for example `CMD ["echo","hi"]`, or a script that ends, or a background service started with `&`, `nohup` or `service start`). |
| Exit code 1 | App crashed: bad config, cannot connect to DB, missing env variable, port already used inside the container. |
| Exit code 125, 126, 127 | 125: Docker error. 126: command found but not executable (permission). 127: command not found (wrong path, missing shell or binary, wrong architecture). |
| Exit code 137 | `SIGKILL`: out of memory (`OOMKilled`) or manual kill. |
| Exit code 143 | `SIGTERM`: stopped normally. |
| `exec format error` | Image built for another CPU architecture (for example built on Apple Silicon ARM and run on x86 AWS servers). |

```bash
docker ps \
  -a
# Shows the STATUS, for example: Exited (1) 5 seconds ago

docker logs \
  backend
# Shows the error printed by the app before it exited

docker inspect \
  --format '{{.State.ExitCode}} {{.State.OOMKilled}} {{.State.Error}}' \
  backend
# Example output: 1 false      (exit code, OOM flag, Docker error message)

docker run \
  --rm \
  -it \
  --entrypoint sh \
  myapp:1.0
# Starts a shell in the image instead of the app, so you can check files and run the command by hand
```

**Follow-up questions:**

- **Why does `CMD ["service","nginx","start"]` exit?** It starts a background daemon and returns. PID 1 ends, so the container stops. Run the program in the foreground: `nginx -g "daemon off;"`.
- **How do you build for another architecture?** `docker buildx build --platform linux/amd64 ...` (BuildKit's `buildx`). Multi-arch images can be built with `--platform linux/amd64,linux/arm64`.
- **Windows line endings in a shell script?** A script with CRLF line endings fails with "no such file or directory" because of `\r` in the shebang line. Convert to LF.

---

### Q35. [Advanced] How does the JVM behave in a container, and how do you set memory correctly?

**Short answer:** Modern JVMs read the container's cgroup limits, so heap size defaults to a fraction (25%) of the container memory limit. Set `-XX:MaxRAMPercentage` rather than a fixed `-Xmx`, and leave room for non-heap memory so the container does not get OOMKilled.

**Explanation:**

- Container awareness: Java 10+ and Java 8u191+ detect cgroup limits (version-specific). Older JVMs read the host's memory and can set a heap larger than the container limit, causing OOM kills.
- Defaults: `MaxRAMPercentage` is 25% of the available memory. For a dedicated app container, 60 to 75% is common.
- JVM memory = heap + metaspace + thread stacks + code cache + direct buffers + GC and native overhead. The container limit must cover all of it, not only the heap.
- If you set `-Xmx` equal to the container limit, the total will exceed the limit and the kernel will kill the process (exit code 137).
- CPU: the JVM sets the number of GC and compiler threads and `Runtime.availableProcessors()` from the CPU limit. With `--cpus 0.5` the JVM sees 1 processor. A very small CPU limit can slow startup and cause long pauses.
- If memory behavior differs between JVM versions, check cgroup v2 support (supported from JDK 15 and backported to 11.0.16 and 8u372) (version-specific).

```dockerfile
ENV JAVA_TOOL_OPTIONS="-XX:MaxRAMPercentage=70.0 -XX:InitialRAMPercentage=50.0 -XX:+ExitOnOutOfMemoryError"
```

```bash
docker run \
  --rm \
  --memory 1g \
  eclipse-temurin:17-jre \
  java \
  -XX:+PrintFlagsFinal \
  -version \
| grep -E "MaxHeapSize|MaxRAMPercentage"
# Example: MaxHeapSize is about 256 MB with the default 25% of 1 GB (exact value varies)

docker exec \
  backend \
  jcmd 1 VM.native_memory summary
# Needs the JVM started with -XX:NativeMemoryTracking=summary. Shows non-heap memory use.
```

```text
Container limit: 1 GB
+------------------------------------------+
| Heap (70% = 700 MB)                      |
+------------------------------------------+
| Metaspace + thread stacks + code cache   |
| + direct buffers + native overhead       |  <- must fit in remaining 300 MB
+------------------------------------------+
```

**Follow-up questions:**

- **Pod gets OOMKilled but the heap graph looks fine. Why?** Non-heap memory (threads, metaspace, direct buffers, native libraries) grew. Lower the heap percentage, set `-XX:MaxMetaspaceSize`, limit thread counts, or raise the limit.
- **Why is `-Xms` equal to `-Xmx` sometimes used?** The heap is allocated at start (predictable, no resize pauses), but it uses that memory immediately. Fine for stable services.
- **Which GC for containers?** G1 is the default for larger heaps. With less than 2 CPUs or less than about 1792 MB RAM, the JVM picks SerialGC automatically (the "not a server-class machine" rule). You can force the choice with `-XX:+UseG1GC` or `-XX:+UseSerialGC`.

---

### Q36. [Advanced] How do you secure Docker images and containers?

**Short answer:** Run as a non-root user, use small trusted base images, scan images for vulnerabilities, keep secrets out of images, drop capabilities, use read-only filesystems where possible, pin versions, and keep the host and Docker updated.

**Explanation:**

| Practice | What it does |
|---|---|
| Non-root `USER` | If the app is compromised, the attacker does not have root inside the container. |
| Minimal base image (distroless, alpine, slim) | Fewer packages mean fewer vulnerabilities and fewer tools for an attacker. |
| Image scanning | Finds known CVEs (publicly listed vulnerabilities) in OS packages and libraries. Tools: Trivy, Grype, Docker Scout, AWS ECR scanning, Snyk. Run it in CI and fail the build on high severity. |
| Pin and update base images | Use specific versions (or digests) and rebuild regularly to get patches. |
| No secrets in images | See Q32 and build secrets below. |
| `--read-only` filesystem | The container cannot write to its filesystem. Use `--tmpfs /tmp` for needed temp space. |
| `--cap-drop ALL` | Removes Linux capabilities and adds back only what is needed. |
| `--security-opt no-new-privileges` | Prevents gaining more privileges through setuid binaries. |
| Resource limits | Stops one container from using all host resources (Q31). |
| Avoid `--privileged` and `docker.sock` mounts | Both can give host-level access. |
| Trusted sources, image signing | Pull from official or verified images. Sign with Cosign or Docker Content Trust. |
| Rootless Docker | The Docker daemon itself runs without root privileges. |

```bash
docker run \
  -d \
  --name backend \
  --read-only \
  --tmpfs /tmp \
  --cap-drop ALL \
  --security-opt no-new-privileges \
  --user 1000:1000 \
  --memory 1g \
  --pids-limit 200 \
  myapp:1.0
# Hardened container: read-only filesystem, no extra capabilities, non-root, limited resources

trivy image \
  --severity HIGH,CRITICAL \
  --exit-code 1 \
  myapp:1.0
# Scans the image. Exit code 1 if any HIGH or CRITICAL finding exists, so a CI job fails.
```

**Build secrets with BuildKit** (for example a private Maven repository token) so the secret does not end up in a layer:

```dockerfile
# syntax=docker/dockerfile:1
FROM eclipse-temurin:17-jdk AS builder
WORKDIR /workspace
COPY . .
RUN --mount=type=secret,id=mvn_settings,target=/root/.m2/settings.xml \
    ./mvnw -q package -DskipTests
```

```bash
docker build \
  --secret id=mvn_settings,src=$HOME/.m2/settings.xml \
  -t myapp:1.0 \
  .
# The secret is available only during that RUN step and is not stored in the image
```

**Follow-up questions:**

- **Is running as root in a container really dangerous?** Container root is not the same as host root, but a breakout or a mounted host path gives an attacker much more power. Treat it as unsafe.
- **How do you handle a non-root user writing to a mounted volume?** Set the volume owner to match the container user ID (`chown` on the host, `COPY --chown`, or Kubernetes `fsGroup`).
- **How do you handle a vulnerability found in the base image?** Update the base image tag, rebuild, rescan, redeploy. If there is no fix yet, assess whether it is reachable and document an exception.
- **What is an SBOM?** A software bill of materials: a list of all packages inside an image. Tools like Syft or `docker buildx build --sbom=true` generate it (version-specific).

---

### Q37. [Advanced] How would you build and push Docker images in a Jenkins pipeline?

**Short answer:** In a Jenkins pipeline, check out the code, build and test, run `docker build` with a unique tag (version plus commit), scan the image, log in to the registry with stored credentials, `docker push`, then trigger the deployment with the same tag.

**Explanation:**

- Credentials are stored in Jenkins (Credentials plugin) and injected with `withCredentials`. They are never hardcoded.
- The Jenkins agent needs access to a Docker daemon. Options: Docker installed on the agent, a Docker-in-Docker setup, a mounted `docker.sock` (high risk), or daemonless builders (Kaniko, Buildah, Jib) which are safer in Kubernetes-based agents.
- Tag with `BUILD_NUMBER` and Git commit for traceability. Deploy that exact tag.
- Use `post { always { ... } }` to log out and clean up local images to avoid filling the agent disk.

```groovy
pipeline {
  agent any

  environment {
    REGISTRY = '123456789012.dkr.ecr.eu-west-1.amazonaws.com'
    IMAGE    = "${REGISTRY}/myapp"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
        script {
          env.GIT_SHORT = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
          env.TAG = "${env.BUILD_NUMBER}-${env.GIT_SHORT}"
        }
      }
    }

    stage('Build image') {
      steps {
        sh 'docker build -t ${IMAGE}:${TAG} .'
        // Tests run inside the Dockerfile test stage or a separate Maven stage
      }
    }

    stage('Scan') {
      steps {
        sh 'trivy image --severity HIGH,CRITICAL --exit-code 1 ${IMAGE}:${TAG}'
      }
    }

    stage('Push') {
      steps {
        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',
                          credentialsId: 'aws-ecr-creds']]) {
          sh '''
            aws ecr get-login-password --region eu-west-1 \
              | docker login --username AWS --password-stdin ${REGISTRY}
            docker push ${IMAGE}:${TAG}
          '''
        }
      }
    }

    stage('Deploy') {
      when { branch 'main' }
      steps {
        sh 'kubectl set image deployment/myapp myapp=${IMAGE}:${TAG}'
        // or update a Helm value / GitOps repo with the new tag
      }
    }
  }

  post {
    always {
      sh 'docker logout ${REGISTRY} || true'
      sh 'docker rmi ${IMAGE}:${TAG} || true'
    }
  }
}
```

```text
Git push -> Jenkins -> build + test -> docker build -> scan -> push to ECR
                                                                  |
                      deploy (kubectl / Helm / ECS) <-- same tag --+
```

**Follow-up questions:**

- **Why not tag only `latest` in CI?** Not traceable, no safe rollback (Q5).
- **How do you speed up image builds in CI?** Use BuildKit cache (`--cache-from`, `--cache-to` with a registry), or a persistent agent with a warm layer cache, and layered jars (Q20).
- **How do you avoid running a privileged Docker daemon in Jenkins on Kubernetes?** Use Kaniko, Buildah or Jib, which build images without a Docker daemon.
- **How does rollback work?** Redeploy the previous immutable tag: `kubectl rollout undo deployment/myapp`, or deploy the earlier tag.
- **Docker agent in a pipeline?** Jenkins can run a stage inside a container (`agent { docker { image 'maven:3.9-eclipse-temurin-17' } }`) so the build tool version is controlled in code.

---

### Q38. [Advanced] A container keeps getting OOMKilled. How do you investigate?

**Short answer:** Confirm it was an OOM kill (exit code 137 plus `OOMKilled: true`), then find whether the limit is too small, the app leaks memory, or the JVM heap is set too close to the limit. Fix by tuning JVM memory, fixing leaks, or raising the limit.

**Explanation:**

Steps:

1. Confirm the cause.
2. Watch memory over time (`docker stats`, Prometheus and cAdvisor, CloudWatch Container Insights).
3. Compare the limit with the JVM settings. Heap plus non-heap must be below the limit (Q35).
4. Take a heap dump if heap is growing (`-XX:+HeapDumpOnOutOfMemoryError`) and analyze it (Eclipse MAT, VisualVM).
5. Check native memory (`-XX:NativeMemoryTracking`), thread count, direct buffers.
6. Fix: reduce heap percentage, limit threads and caches, fix the leak, then raise the limit if the app really needs it.

```bash
docker inspect \
  --format 'ExitCode={{.State.ExitCode}} OOMKilled={{.State.OOMKilled}}' \
  backend
# Example output: ExitCode=137 OOMKilled=true

docker stats \
  --no-stream \
  backend
# Example: memory usage close to the limit (for example 998MiB / 1GiB)

dmesg -T | grep -i "killed process"
# On the host: kernel log lines showing which process was killed for memory
```

```bash
kubectl describe pod myapp-7d9c8b6f5-abcde
# In Kubernetes: look for "Last State: Terminated, Reason: OOMKilled, Exit Code: 137"
```

**Two different "out of memory" events:**

| Event | Who kills | Signs |
|---|---|---|
| `java.lang.OutOfMemoryError` | The JVM (heap is full) | Stack trace in logs. JVM may keep running or exit. |
| `OOMKilled` | The Linux kernel (cgroup limit reached) | No Java error. Exit code 137. Logs just stop. |

**Follow-up questions:**

- **Heap is only 50% used but the container is killed. Why?** Non-heap memory pushed the total over the limit. Check metaspace, thread stacks, direct memory, and native libraries.
- **Should you just raise the limit?** Only if monitoring shows the app really needs it. Otherwise you hide a leak and waste money.
- **Kubernetes: what is the difference between requests and limits?** The scheduler uses `requests` to place the pod. The kernel enforces `limits`. A pod over its memory limit is killed. A pod over its CPU limit is throttled.

---

### Q39. [Advanced] The host disk is full because of Docker. How do you find and fix it?

**Short answer:** Run `docker system df` to see what uses space (images, containers, volumes, build cache). Then prune what is unused. Also check container log files, which grow without limit by default.

**Explanation:**

Typical causes: old images (from CI builds on an agent), stopped containers, unused volumes, build cache, and large `json-file` logs.

```bash
docker system df
# Summary of space used by images, containers, volumes and build cache

docker system df \
  -v
# Detailed list per image, container and volume

docker container prune
# Removes stopped containers

docker image prune \
  -a \
  --filter "until=168h"
# Removes unused images older than 7 days

docker builder prune
# Removes build cache

docker volume prune
# Removes volumes not used by any container. PERMANENT data loss if a volume held data.

docker system prune
# Removes stopped containers, unused networks, dangling images and build cache (not volumes)

du -sh /var/lib/docker/containers/*/*-json.log | sort -h | tail
# Linux: finds the biggest container log files
```

Prevent a repeat:

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

This is `/etc/docker/daemon.json` on Linux. Restart the Docker daemon for it to apply. It affects containers created after the change.

- Prune old images regularly on CI agents (a scheduled job).
- Use ECR lifecycle policies to delete old registry images.
- Write logs to stdout and ship them to a central system (CloudWatch, ELK, Loki).
- Use separate disks or volumes for `/var/lib/docker` on servers.

**Follow-up questions:**

- **Is `docker system prune -a` safe on a server?** It removes all images not used by a running container. Anything stopped will need to re-pull. It does not remove volumes unless you add `--volumes`.
- **What does "dangling" mean vs "unused"?** Dangling = untagged. Unused = not referenced by any container. `prune -a` removes unused images, not only dangling ones.
- **Container writable layer growing?** Something writes files inside the container (temp files, logs). Use a volume or `--tmpfs`, or fix the app.

---

### Q40. [Advanced] Kubernetes basics: what are a Pod, Deployment, Service and Ingress?

**Short answer:** A **Pod** is the smallest unit: one or more containers sharing a network and storage. A **Deployment** keeps a set number of identical Pods running and handles rolling updates. A **Service** gives Pods a stable name and IP and load balances. An **Ingress** routes external HTTP(S) traffic to Services by host and path.

**Explanation:**

| Object | What it is or does |
|---|---|
| Pod | One or more containers that share an IP address and volumes. Pods are temporary: when a Pod dies, it is replaced with a new one that has a new IP. |
| ReplicaSet | Keeps N copies of a Pod running. Created and managed by a Deployment. |
| Deployment | Describes the desired state (image, number of replicas). Does rolling updates and rollbacks. |
| Service | Stable virtual IP and DNS name in front of a group of Pods (found by labels). Types: `ClusterIP` (internal only, default), `NodePort`, `LoadBalancer` (cloud load balancer, for example AWS ELB). |
| Ingress | Rules for HTTP(S) routing from outside to Services (for example `/api` to the backend Service). Needs an Ingress controller such as NGINX Ingress or the AWS Load Balancer Controller. |
| Node | A machine (VM) that runs Pods. |
| Namespace | A logical group of objects inside a cluster, used to separate teams or environments. |

```text
Internet
   |
 Ingress (host/path rules)
   |
 Service "backend" (stable name, load balancing)
   |-------------+-------------+
 Pod backend-1  Pod backend-2  Pod backend-3   <- managed by a Deployment
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: registry.example.com/team/myapp:1.4.2-a1b2c3d
          ports:
            - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 8080
```

```bash
kubectl apply \
  -f deployment.yml
# Creates or updates the objects described in the file

kubectl get pods
# Lists Pods and their status

kubectl rollout undo \
  deployment/backend
# Rolls back to the previous version of the Deployment
```

**Follow-up questions:**

- **Docker port mapping vs Kubernetes Service?** Port publishing maps a host port to one container. A Service load balances across many Pods and gives a stable name. `targetPort` is the container port, `port` is the Service port.
- **How does a rolling update work?** The Deployment starts new Pods and stops old ones step by step, controlled by `maxSurge` and `maxUnavailable`, and only sends traffic to a new Pod after its readiness probe passes.
- **Why Deployment instead of a bare Pod?** A bare Pod is not recreated if its node fails and has no rolling updates.

---

### Q41. [Advanced] Kubernetes basics: ConfigMap, Secret, probes and HPA.

**Short answer:** A **ConfigMap** holds non-secret configuration and a **Secret** holds sensitive values; both are injected as environment variables or files. **Probes** let Kubernetes check container health (liveness, readiness, startup). The **HPA** (Horizontal Pod Autoscaler) changes the number of Pods based on metrics like CPU.

**Explanation:**

| Object | What it does |
|---|---|
| ConfigMap | Key-value configuration, outside the image. Change config without rebuilding. |
| Secret | Like a ConfigMap but for sensitive data. Base64-encoded, not encrypted by default (see Q32). |
| Liveness probe | "Is the container stuck?" If it fails, Kubernetes restarts the container. |
| Readiness probe | "Can it take traffic?" If it fails, the Pod is removed from the Service endpoints (no restart). |
| Startup probe | "Has it finished starting?" Pauses the other probes until it passes. Useful for slow Spring Boot startup. |
| HPA | Scales Deployment replicas up or down by CPU, memory or custom metrics. Needs the metrics server. Needs CPU `requests` to be set for CPU percentage targets. |

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: backend-config
data:
  SPRING_PROFILES_ACTIVE: prod
---
apiVersion: v1
kind: Secret
metadata:
  name: backend-secret
type: Opaque
stringData:
  SPRING_DATASOURCE_PASSWORD: change-me       # example only; do not commit real values
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
        - name: backend
          image: registry.example.com/team/myapp:1.4.2-a1b2c3d
          envFrom:
            - configMapRef:
                name: backend-config
            - secretRef:
                name: backend-secret
          resources:
            requests:
              cpu: 250m
              memory: 512Mi
            limits:
              memory: 1Gi
          startupProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
            failureThreshold: 30
            periodSeconds: 5
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8080
            periodSeconds: 10
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8080
            periodSeconds: 5
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: backend
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: backend
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

`autoscaling/v2` is the stable HPA API from Kubernetes 1.23 (version-specific).

**Follow-up questions:**

- **Liveness probe failing too early restarts the app in a loop. Why?** The app is still starting (Spring Boot can be slow). Use a `startupProbe` or a larger `initialDelaySeconds`.
- **Should the readiness probe check the database?** Often yes for readiness (do not send traffic if the DB is down), but be careful with liveness: a database outage should not restart all your Pods.
- **Does changing a ConfigMap restart Pods?** Not by itself. Env vars are read at start, so a rollout is needed (`kubectl rollout restart deployment/backend`). Mounted files update after some delay, but the app must reload them.
- **How does HPA relate to resource requests?** Utilization is a percentage of the CPU request, so a missing or wrong request gives wrong scaling.

