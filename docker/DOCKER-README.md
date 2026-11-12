# Docker — The Complete Guide (interview prep)

This file explains Docker from the ground up, with real examples, plus the extra concepts
interviewers love to ask about.

Written in plain English. No jargon without an explanation next to it.

---

## Table of Contents

1. [What even is Docker? (ELI5)](#1-what-even-is-docker-eli5)
2. [The core vocabulary](#2-the-core-vocabulary)
3. [Working with images and containers: run, list, stop, remove](#3-working-with-images-and-containers-run-list-stop-remove)
4. [Dockerfile — explained line by line](#4-dockerfile--explained-line-by-line)
5. [docker-compose.yml — explained line by line](#5-docker-composeyml--explained-line-by-line)
6. [Ports — what `3010:3000` actually means](#6-ports--what-30103000-actually-means)
7. [Docker CLI reference (cleanup, volumes, networks, login)](#7-docker-cli-reference-cleanup-volumes-networks-login)
8. [All the docker-compose CLI commands needed](#8-all-the-docker-compose-cli-commands-needed)
9. [Volumes vs Bind Mounts (this trips everyone up)](#9-volumes-vs-bind-mounts-this-trips-everyone-up)
10. [Networking — how containers talk to each other](#10-networking--how-containers-talk-to-each-other)
11. [Multi-stage builds — why Dockerfiles have "AS builder"](#11-multi-stage-builds--why-dockerfiles-have-as-builder)
12. [Extra concepts interviewers ask about](#12-extra-concepts-interviewers-ask-about)
13. [Quick interview cheat-sheet (rapid fire Q&A)](#13-quick-interview-cheat-sheet-rapid-fire-qa)

---

## 1. What even is Docker? (ELI5)

Think about it like this: **"It works on my machine" is the most annoying sentence in software.**
One machine has Java 17, another has Java 11, a server somewhere has Java 21 — and the app breaks
in different ways on each one.

Docker's whole pitch is: **package the app AND everything it needs (the exact OS bits, the exact
runtime, the exact libraries) into one sealed box.** That box is called a **container**. That box
can ship to any machine — a laptop, a teammate's laptop, AWS, whatever — and it runs *identically*
every time, because it's not using the host machine's Java/Node/whatever. It's using its own,
bundled inside.

**Analogy:** A shipping container. Doesn't matter if it's going on a ship, a train, or a truck —
the container's contents don't change, and the vehicle doesn't need to know what's inside. Docker
does this for software: the app doesn't care if it's running on Windows or Linux underneath,
because it's carrying its own tiny "operating system slice" with it.

**Container vs Virtual Machine — the classic question:**
- A **VM** virtualizes an entire computer, including its own full OS kernel. Heavy, slow to boot
  (minutes), each VM might be gigabytes.
- A **container** shares the host machine's OS kernel and only packages the app + its dependencies.
  Lightweight, boots in seconds, images are usually megabytes.
- Rule of thumb answer: *"VMs virtualize hardware, containers virtualize the OS."*

---

## 2. The core vocabulary

These words get thrown around interchangeably by beginners, but they mean specific, different things:

| Term | Plain-English meaning |
|---|---|
| **Image** | A read-only *blueprint/recipe* for a container. Like a class in OOP, or a recipe for a cake. It never runs by itself. |
| **Container** | A *running instance* of an image. Like an object created from a class, or the actual cake baked from the recipe. Many containers can run from the same image. |
| **Dockerfile** | A text file with instructions for *building* an image (step by step: "start from this base, copy these files, run this command..."). |
| **Docker Compose** | A tool + YAML file (`docker-compose.yml`) that defines and runs **multiple containers together** (e.g. a database + backend + frontend) with one command, instead of typing long `docker run` commands for each. |
| **Registry** | A place that stores and distributes images, e.g. **Docker Hub**. `docker push`/`docker pull` talk to a registry. |
| **Volume** | Persistent storage that lives *outside* the container's own filesystem, so data survives even if the container is deleted. Databases typically use this. |
| **Network** | A virtual private network Docker creates so containers can find and talk to each other by name (e.g. a frontend calling `http://backend:8090` — `backend` is not a real DNS name anywhere, Docker made it up). |
| **Layer** | Each instruction in a Dockerfile (`COPY`, `RUN`, etc.) creates a "layer" — a cacheable, stacked filesystem diff. This is why layer *order* in a Dockerfile matters a lot for build speed. |
| **Tag** | A label on an image, like a version number, e.g. `myapp:latest` or `myapp:v1.2`. `latest` is just a tag, not magic — it doesn't auto-update. |

---

## 3. Working with images and containers: run, list, stop, remove

This is the hands-on loop: "there's a Docker image — now what?" Everything below assumes the
image already exists (either built locally or pulled from a registry).

A quick note on command style before starting: since Docker 1.13, the CLI is organized into
object-based groups — `docker container ...`, `docker image ...`, `docker network ...`,
`docker volume ...`. The older short forms (`docker run`, `docker ps`, `docker images`) still work
and are just aliases. Both are shown below; either is fine to use.

### Step 0 — anatomy of an image name

Every image reference follows this shape: `[registry/]namespace/repository[:tag]`. Take a real
one apart:

```
in28min / hello-world-java : 0.0.1.RELEASE
   ↑              ↑                  ↑
namespace     repository            tag
(publisher's   (the image        (version/build
 Docker Hub     name itself)      label)
 username)
```

- **`in28min`** — the namespace: the Docker Hub username/organization that published this image.
  An image with *no* namespace (like plain `nginx` or `mysql`) is an official Docker-maintained
  image, not published under a personal account.
- **`hello-world-java`** — the repository name: what a human calls "the image."
- **`0.0.1.RELEASE`** — the **tag**: a label for a specific build/version of that repository. A
  publisher can tag the same underlying image `latest`, `v1`, `alpine`, `0.0.1.RELEASE` — whatever
  they choose. Leave the tag off entirely (just `in28min/hello-world-java`) and Docker assumes
  `:latest`.
- There's technically a registry prefix too (`docker.io/in28min/hello-world-java:...`) — it's
  omitted here because Docker Hub is the default registry when none is specified.
- **Why the long name?** Namespacing by publisher avoids collisions — anyone could publish an
  image called `hello-world`, so `namespace/repository:tag` is what uniquely identifies exactly
  which image, from whom, at which version.

### Step 1 — get an image

```bash
docker pull in28min/hello-world-java:0.0.1.RELEASE   # download the image from Docker Hub — doesn't run it yet
docker build -t myapp:latest .                         # OR build one locally from a Dockerfile in the current folder
```

### Step 2 — see what images exist

```bash
docker image ls     # modern form — list every image stored locally: name, tag, size, created date
docker images       # older alias for "docker image ls" — identical result, just the pre-1.13 command name
docker images -a    # also include intermediate/dangling (untagged) images
```

### Step 3 — run a container from that image

Worked example — run the image just pulled above:

```bash
docker container run -d -p 8080:5000 in28min/hello-world-java:0.0.1.RELEASE
```

- **`docker container run`** — modern form of `docker run`. Identical behavior, just spelled out
  under the `container` command group.
- **`-d`** — detached. Runs in the background and hands the terminal back immediately, instead of
  streaming logs and blocking it.
- **`-p 8080:5000`** — `HOST_PORT:CONTAINER_PORT`.
  - Left `8080` = the port on the local machine — this is what gets typed into a browser:
    `http://localhost:8080`.
  - Right `5000` = the port the Java app is listening on *inside* the container (whatever port
    that app's code binds to — for this image, 5000). They don't have to match, and here they
    deliberately don't, to make that clear.
  - **Is this a two-way tunnel?** Not really — it's one-way port *forwarding*, not a tunnel. Docker
    sets up a NAT rule: "traffic hitting the host's port 8080 gets forwarded into this container's
    internal network, to its port 5000." The container never knows or cares what host port was
    chosen — it only ever sees its own internal `5000`. Responses simply flow back along that same
    connection, so no separate reverse rule is needed.

> **Gotcha — the container-side port is only a forwarding target, not a guarantee.**
>
> Quick reminder: `-p` (short for `--publish`) is the flag that sets up `HOST_PORT:CONTAINER_PORT`
> forwarding on `docker run`/`docker container run` — that's the only thing it does.
>
> **Scenario:** container run with `-p 8080:5000`, but the Spring Boot app's
> `application.properties` has `server.port=1010`.
>
> **Question:** does `localhost:8080` reach the app?
>
> **Short answer: no — it won't work.**
>
> **Why:** Docker never inspects the app inside the container; it just wires up "host port →
> this container port" and assumes something is listening there. Traffic to `localhost:8080` gets
> forwarded to the container's port `5000` — where **nothing is listening**, because Spring Boot
> only opened port `1010`. Result: connection refused, even though the container is "running." The
> container-side number in `-p` has to be manually kept in sync with whatever port the app
> actually binds to (`-p 8080:1010` would fix it here) — Docker does not do that alignment
> automatically.

> **Related question — does the Dockerfile's `EXPOSE` value need to match `-p`?**
>
> **Scenario:** the image's Dockerfile has `EXPOSE 2020`, but the real Spring Boot app has
> `server.port=5000`, and the container is run with `-p 8080:5000`. Does `localhost:8080` reach
> the app?
>
> **Short answer: yes — it still reaches it. The wrong `EXPOSE` value changes nothing here.**
>
> **Why:** `EXPOSE` is **pure documentation**. Docker doesn't enforce it, doesn't check it against
> what the app actually does, and doesn't use it to set up any real forwarding. It's a note left by
> whoever wrote the Dockerfile — nothing more. The only thing that creates an actual working
> connection is `-p HOST:CONTAINER` at `docker run` time, which is set independently and doesn't
> need to agree with `EXPOSE` at all. So `EXPOSE` and `-p` are **not** an either/or — `-p` is the
> only one doing real work; `EXPOSE` is metadata that's easy to leave stale without breaking
> anything.
>
> **The one exception:** `docker run -P` (capital `P`, "publish all exposed ports") tells Docker to
> auto-pick host ports based on the image's `EXPOSE` list. In that specific case, a wrong `EXPOSE`
> value really would cause `-P` to publish the wrong port. With an explicit `-p 8080:5000` like
> this scenario, though, `EXPOSE`'s value is simply irrelevant.

Other useful flags, same idea, different examples:

```bash
docker run -d --name myapp-1 myapp                  # --name gives it a friendly name instead of a random one like "brave_einstein"
docker run -d -e SPRING_PROFILES_ACTIVE=dev myapp   # -e sets an environment variable inside the container
docker run -d -v mydata:/var/lib/mysql mysql:8.0    # -v mounts a volume (or bind mount) into the container
docker run -it ubuntu bash                          # -it = interactive + a terminal attached, drops into a live shell
docker run --rm myapp                               # --rm auto-deletes the container the instant it stops (great for throwaway/test runs)
```

Flags combine freely, e.g.:
```bash
docker run -d --name backend -p 8090:8090 -e SPRING_PROFILES_ACTIVE=dev -v uploads:/app/uploads myapp
```

**Common flag cheat table:**

| Flag | Meaning |
|---|---|
| `-d` | detached — run in the background |
| `-p host:container` | publish a port to the host |
| `-e KEY=value` | set an environment variable |
| `-v name:/path` or `-v ./host-path:/path` | mount a named volume or a bind mount |
| `--name` | give the container a fixed, friendly name |
| `--rm` | auto-remove the container as soon as it stops |
| `-it` | interactive terminal (`-i` keeps stdin open, `-t` allocates a pseudo-terminal) |
| `--restart unless-stopped` | restart policy, directly on `docker run` (compose equivalent: `restart:`) |

### Step 4 — see what's running

```bash
docker container ls      # modern form — list RUNNING containers only
docker ps                # older alias for "docker container ls" — identical result, just the pre-1.13 command name
docker container ls -a   # all containers, running or stopped
docker ps -a             # older alias for "docker container ls -a" — same result
```

### Step 5 — check on it

```bash
docker logs myapp-1          # see what it printed (stdout/stderr)
docker logs -f myapp-1       # keep streaming new log lines live, like tail -f
```

### `docker exec` — running commands inside a running container

**The scenario:** a container is running, but something looks off. Maybe the app isn't responding
the way it should, maybe there's doubt about whether a config file actually got copied into the
image correctly, maybe an environment variable needs double-checking. There's no file browser
into a container from the outside — the only way to look is to ask the container itself to run a
command and report back. That's exactly what `docker exec` is for: *"run this one command, inside
this specific already-running container, right now."*

General shape:
```bash
docker exec [flags] <container> <command>
```

**Scenario 1 — a quick one-off check (no shell needed):**
```bash
docker exec backend ls /app                            # see what files actually exist inside the container
docker exec backend cat /app/application.properties     # read a config file's real contents inside the container
docker exec backend env                                 # see every environment variable the container actually has
docker exec backend ps aux                               # see what processes are running inside the container
```
Each of these runs **one** command inside the container, prints the result to the terminal, and
exits immediately. No `-it` needed — nothing interactive is happening, it's a single question with
a single answer.

**Scenario 2 — poking around interactively:**
```bash
docker exec -it backend sh     # open a live shell inside the container
docker exec -it backend bash   # same idea, if the image actually has bash installed (many minimal images like Alpine only ship sh)
```
Now there's a live prompt inside the container — `cd` around, run several commands back to back,
until typing `exit` (or pressing **Ctrl+D**) disconnects.

**What actually happens on `exit`:** since this shell was reached via `exec` — a side session
attached to a container that was already running something else — typing `exit` only ends that
shell process. The terminal detaches and drops back to the normal host prompt, but the container
itself is untouched and keeps running exactly as before. Running `docker container ls` right after
still shows it as running.

> **Careful — this is different from `docker run -it ... sh`.** If a container's *main* process
> (the one it was originally started with) is the shell itself, then `exit` ends that process —
> and since a container stops the instant its main process ends, the whole container stops too.
> The rule of thumb: `exit`ing an `exec` shell only closes a visiting session; `exit`ing a
> container's actual main process stops the container.

**Flags explained:**

| Flag | Meaning |
|---|---|
| `-i` | interactive — keeps stdin open so typed input actually reaches the command |
| `-t` | allocates a pseudo-terminal (TTY) — makes it behave like a normal interactive terminal, with a real prompt |
| `-it` | `-i` and `-t` combined — the standard pairing for "give me a live shell" |
| `-u <user>` | run this command as a specific user inside the container instead of its default user, e.g. `-u root` |
| `-w <path>` | set the working directory for just this one command, e.g. `-w /app` |
| `-e KEY=value` | set an extra environment variable, visible only to this exec'd command |

**Common real-world uses:**

| Command | Why run it |
|---|---|
| `docker exec -it backend sh` | debug interactively, look around the filesystem live |
| `docker exec backend cat /app/logs/app.log` | check a log file's contents once, without streaming |
| `docker exec backend env` | verify an environment variable actually got set the way it's expected to |
| `docker exec -u root backend sh` | get root access inside a container that normally runs as a non-root user (e.g. to fix a file permission issue) |
| `docker exec backend ping other-service` | test whether this container can actually reach another one over the Docker network |

**Important limitation:** `docker exec` only works on a container that is **already running**. If
it's stopped, `docker start` it first — `exec` cannot start a stopped container, and it never
creates a new one (that's what `docker run` is for).

### Step 6 — stop / start / remove ("up and down")

```bash
docker stop myapp-1      # graceful stop (SIGTERM, then SIGKILL after a grace period)
docker start myapp-1     # start that same container again, same settings as before
docker restart myapp-1   # stop then start
docker rm myapp-1        # delete the (stopped) container entirely
docker rm -f myapp-1     # force-delete even if it's still running
```

**Interview one-liner:** *"`docker run` creates AND starts a new container from an image in one
step. `docker start`/`docker stop` only affect a container that already exists — they don't
create anything new."*

---

## 4. Dockerfile — explained line by line

### 4a. Common Dockerfile instructions, in plain terms

Before diving into a real Dockerfile, here are the instructions that show up in almost every one
of them, explained in the simplest way possible:

| Instruction | In plain terms |
|---|---|
| `FROM` | "Start from this existing image instead of nothing." Every Dockerfile begins here — it's picking a base to build on top of (e.g. a Linux distro with Java pre-installed), instead of starting from a totally empty, OS-less box. |
| `WORKDIR` | "From now on, treat this folder as the current folder." Like typing `cd /app` once so every instruction after it (`COPY`, `RUN`, etc.) doesn't need to repeat the full path. If the folder doesn't exist yet, Docker creates it. |
| `COPY` | "Take this file/folder from my computer and place it inside the image." That's it — a straightforward file copy from the build context (the folder being built) into the image being built. |
| `RUN` | "Execute this command *while building the image*, and bake the result into it." E.g. `RUN npm install` actually installs packages during the build, so they're already sitting inside the image when it's done. |
| `CMD` | "This is the default thing to do when a container starts from this image" — but it can be overridden by whoever runs the container. |
| `ENTRYPOINT` | Similar to `CMD`, but meant to be the fixed, hard-to-override "main purpose" of the container. Often paired with `CMD`, where `CMD` just supplies default arguments to it. |
| `ENV` | "Set this environment variable, and keep it set for anyone who runs a container from this image." Persists into the running container. |
| `EXPOSE` | "Documentation: this container listens on this port internally." Doesn't actually publish anything to the host — just a note to humans/tooling. |
| `USER` | "From this point on, run as this user instead of root." A security choice — don't let the app run with full root privileges inside the container if it doesn't need to. |
| `ARG` | "A variable usable only during the build itself" (e.g. `docker build --build-arg VERSION=1.2 .`). Gone once the image exists — the opposite lifespan of `ENV`. |

### RUN vs CMD — in layman's terms

This is one of the most commonly confused pairs, so here's the simplest possible framing:

- **`RUN`** happens **while the image is being built** (`docker build`). It's a one-time action
  that changes what ends up baked inside the image — like a chef prepping ingredients *before* the
  restaurant opens. Every `RUN` line makes the image itself different (usually bigger, with more
  installed).
- **`CMD`** happens **when a container starts from that already-built image** (`docker run`). It
  doesn't change the image at all — it just says "when someone actually runs this, do this by
  default." Like a note on the recipe card saying "serve hot" — it doesn't change what's in the
  kitchen, it just describes what happens at serving time.

**Analogy:** baking a cake.
- `RUN` = mixing and baking the cake — this happens once, in the kitchen, before anyone eats it.
  A Dockerfile can have many `RUN` lines, each one further preparing the image.
- `CMD` = the instruction on the box for how to serve it ("microwave for 30 seconds before eating").
  A Dockerfile normally has exactly **one** `CMD` — it's what happens at the very end, every time
  someone actually "runs" the finished product. If more than one `CMD` is written, only the last
  one takes effect — they don't stack the way `RUN` lines do.

### `RUN` vs `ENTRYPOINT` vs `CMD` — three easily confused instructions

#### `RUN`

`RUN` is used **during image building** to install or prepare things inside the image — installing
`curl`, creating directories, installing packages, or building the application. These commands
execute when `docker build` runs, and whatever they change becomes a permanent part of the final
image.

```dockerfile
RUN apt-get install -y curl
```

#### `ENTRYPOINT`

`ENTRYPOINT` defines the **main program/process** the container runs when it starts — the
container's fixed main executable.

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Whenever a container starts from this image, Docker runs the Spring Boot application. Think of
`ENTRYPOINT` like a bus on a fixed route — it's always going to drive that route no matter what;
it doesn't get swapped out just because something else was typed on `docker run`.

#### `CMD`

`CMD` provides the **default arguments** for `ENTRYPOINT` — think of it like a passenger/parameter
riding along on that bus, not the bus itself. These defaults are easy to override when running the
container.

```dockerfile
CMD ["--server.port=8080"]
```

#### Putting `ENTRYPOINT` + `CMD` together

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
CMD ["--server.port=8080"]
```

runs, by default:

```bash
java -jar app.jar --server.port=8080
```

#### Overriding it at container-start time

```bash
docker container run backend --server.port=9090
```

Whatever gets written after the image name here (`--server.port=9090`) **replaces the Dockerfile's
`CMD` value**, not `ENTRYPOINT`. So the container instead runs:

```bash
java -jar app.jar --server.port=9090
```

The `<cmd>` written after `docker container run <image>` swaps in for `CMD` — it's the "passenger"
that gets replaced. `ENTRYPOINT`, the "bus," keeps running no matter what gets typed there
(overriding `ENTRYPOINT` itself requires the separate `--entrypoint` flag on `docker run`, not just
extra trailing arguments).

#### One more rule worth knowing

If `ENTRYPOINT` or `CMD` is written **multiple times** in the same Dockerfile, only the **last**
one takes effect — they don't stack. This is different from `RUN`, where every `RUN` line executes
in order and each one adds its own layer to the image.

**One-line interview answer:** *"`RUN` builds the image. `ENTRYPOINT` is the fixed process a
container always runs — the bus. `CMD` is the default argument handed to it — the passenger, which
is what gets replaced if something is typed after the image name on `docker run`."*

### A basic, minimal Dockerfile example

Nothing fancy — just enough to see every core instruction working together:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package.json .
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

Walking through it top to bottom: start from a small Node.js base image → treat `/app` as the
working folder → copy in just the dependency manifest → install dependencies (baked into the
image at build time, via `RUN`) → copy in the rest of the source code → document that the app
listens on port 3000 → and finally, define what should happen by default whenever a container is
started from this image (`npm start`).

### Layer caching — what it actually is

Every instruction in a Dockerfile (`FROM`, `COPY`, `RUN`, ...) creates a **layer**: a snapshot of
the filesystem changes that one instruction made, stacked on top of the layer before it — like
transparent sheets stacked to build up a full picture. Docker **caches** each layer. The next time
the same image is built, if a given instruction (and everything before it) hasn't changed at all,
Docker skips re-running it and just reuses the cached layer instantly — instead of redoing that
work from scratch.

The moment one instruction's cache is invalidated (its content changed, or an earlier layer
changed), Docker has to rebuild **that layer and every layer after it** — even if those later
instructions themselves didn't change. This is why the *order* of instructions in a Dockerfile is
a real, practical decision, not just style.

### A simple layer caching scenario

Take the minimal example above. Suppose only a single line of actual application code changes —
nothing about dependencies changed at all. What happens on the next `docker build`?

```dockerfile
FROM node:20-alpine        # unchanged → cache reused instantly
WORKDIR /app                # unchanged → cache reused instantly
COPY package.json .         # package.json itself didn't change → cache reused instantly
RUN npm install              # since package.json's layer was cached, this doesn't even need to re-run — cache reused, dependencies NOT reinstalled
COPY . .                     # source code changed → cache invalidated HERE, this layer re-runs
EXPOSE 3000                  # everything after an invalidated layer re-runs too (though this one does nothing costly)
CMD ["npm", "start"]         # re-runs too (cheap — just metadata)
```

The expensive part (`npm install`, downloading and installing every dependency) is **skipped
entirely**, because nothing before it in the file changed. Only the cheap part (copying updated
source files) actually redoes work. That's the entire reason Dockerfiles copy dependency manifests
(`package.json`, `build.gradle`, `requirements.txt`, ...) and install dependencies *before*
copying the rest of the source code — it turns "install dependencies" into a step that's almost
always served from cache, even though application code changes constantly.

**Now the opposite scenario** — `package.json` itself changes (a new dependency is added):
```dockerfile
COPY package.json .   # package.json changed → cache invalidated HERE
RUN npm install         # this layer is invalidated too, since it comes after → dependencies actually get reinstalled
COPY . .                # also re-runs (comes after an invalidated layer)
```
Everything from the first changed instruction onward re-runs, exactly as expected — cache
invalidation always cascades forward, never backward.

### 4b. Now the real Dockerfiles, line by line

### 4c. Example backend Dockerfile (Spring Boot / Java / Gradle)

```dockerfile
FROM eclipse-temurin:17-jdk-alpine AS builder
```
- `FROM` = "start building on top of this base image." Here it's a small Linux (`alpine`) image
  that already has **Java 17 JDK** installed. JDK = the full toolkit needed to *compile* Java code.
- `AS builder` = names this stage "builder" so it can be referenced later. This is stage 1 of a
  **multi-stage build** (explained in detail in [section 11](#11-multi-stage-builds--why-dockerfiles-have-as-builder)).

```dockerfile
WORKDIR /app
```
- Sets the "current folder" inside the container to `/app`. Every command after this runs from
  there, like doing `cd /app` once instead of before every command.

```dockerfile
COPY gradlew .
COPY gradle gradle/
COPY build.gradle .
COPY settings.gradle .
RUN chmod +x ./gradlew
RUN ./gradlew dependencies --no-daemon
```
- Copies **only the build config files** first (not the actual source code yet) and downloads
  dependencies. This is a deliberate trick for **layer caching**: if only the Java source code
  changes later (not `build.gradle`), Docker can reuse this "dependencies already downloaded"
  layer instead of re-downloading everything on every build. Saves minutes per build.

```dockerfile
COPY src src/
RUN ./gradlew bootJar --no-daemon
```
- *Now* copy the actual source code and compile it into a runnable `.jar` file. This layer changes
  every time the code changes, which is fine — it's cheap compared to re-downloading dependencies.

```dockerfile
FROM eclipse-temurin:17-jre-alpine
```
- Second stage, a **fresh, clean image** — starting over. This one only has the **JRE** (Java
  Runtime Environment — just enough to *run* Java, not compile it), which is much smaller than the
  JDK. This is the whole point of multi-stage builds: throw away the heavy compiler tools, keep
  only the final artifact.

```dockerfile
RUN addgroup -S spring && adduser -S spring -G spring
...
USER spring
```
- Creates a non-root Linux user called `spring` and switches to it. **Security best practice**:
  if the app runs as `root` inside the container and someone finds a vulnerability, they get
  root access on that container. Running as a limited user reduces the blast radius.

```dockerfile
COPY --from=builder /app/build/libs/app-0.0.1-SNAPSHOT.jar app.jar
```
- Pulls **just the compiled jar** out of the `builder` stage above. None of the Gradle cache,
  source code, or build tools make it into the final image — smaller and more secure.

```dockerfile
EXPOSE 8090
```
- **Documentation only** — it tells humans (and some tooling) "this container listens on port
  8090 internally." It does **not** actually publish the port to the host machine. That only
  happens via `-p` on `docker run` or `ports:` in docker-compose. (Very common interview trap!)

```dockerfile
CMD ["java", "-Xms512m", "-Xmx1536m", "-XX:+UseG1GC", ...]
```
- `CMD` = the default command that runs when the container **starts**. Here it launches the Java
  app with tuned memory settings (`-Xms512m` = start with 512MB heap, `-Xmx1536m` = max 1.5GB heap
  — a common sizing choice for a small server).

### 4d. Example frontend Dockerfile (production, Next.js)

Same multi-stage idea, three stages this time: `deps` → `builder` → `runner`.

- **Stage `deps`**: installs npm packages (`npm ci` = "clean install," installs exactly what's in
  `package-lock.json`, no surprises — always prefer this over `npm install` in Docker/CI).
- **Stage `builder`**: copies `node_modules` from `deps`, copies source, runs `npm run build`
  (produces Next.js's optimized production build).
- **Stage `runner`**: fresh minimal image, creates a non-root user `nextjs`, copies **only** the
  built output (`.next/standalone`, `.next/static`, `public`) — not the source code, not
  `node_modules`, not dev dependencies. Final image is tiny compared to shipping everything.
- `EXPOSE 3000` + `ENV PORT 3000` — Next.js listens on 3000 inside the container.
- `HEALTHCHECK` — tells Docker how to ask "are you actually alive?" (not just "did the process
  start"). Docker runs this command on a schedule and marks the container `healthy`/`unhealthy`,
  which `depends_on: condition: service_healthy` (see compose section) can wait on.

### 4e. Example dev Dockerfile

Much simpler, single-stage — because in dev the priority is simplicity and seeing errors fast,
not squeezing out image size. Runs `npm run dev` (hot-reload dev server) instead of the optimized
production build. This is a common pattern: keep **two separate Dockerfiles** for a frontend, one
optimized for shipping, one optimized for a nice developer loop.

### 4f. `.dockerignore`

Same idea as `.gitignore`, but for the Docker build. Anything listed here is **never sent to
Docker** when building the image — so `node_modules`, `.next`, `.git`, markdown docs etc. don't
bloat the build context or accidentally get baked into a layer. Always have one; without it,
`COPY . .` can drag an entire `node_modules` folder into the build context, making builds painfully slow.

---

## 5. docker-compose.yml — explained line by line

A typical setup: a dev compose file and a prod compose file, same shape, different values.

```yaml
version: '3.8'
```
- The Compose file format version. (Newer Docker Compose v2 mostly ignores this and it's
  considered legacy now, but it doesn't hurt to have it.)

```yaml
services:
  mysql:
    image: mysql:8.0
```
- A **service** = one container definition. `image:` means "just pull this ready-made image from
  Docker Hub" — no need to build MySQL from scratch, just reuse the official one.

```yaml
    container_name: myapp-mysql-dev
```
- Gives the container a friendly, fixed name (otherwise Docker invents a random one like
  `myapp_mysql_1`).

```yaml
    env_file:
      - ./backend/.env.dev
```
- Loads environment variables (DB username/password, etc.) from a file instead of hardcoding them
  in the YAML. Keeps secrets out of version control (that file should be gitignored).

```yaml
    ports:
      - "3390:3306"
```
- **`HOST_PORT:CONTAINER_PORT`**. Full breakdown in [section 6](#6-ports--what-30103000-actually-means).

```yaml
    volumes:
      - mysql_data:/var/lib/mysql
```
- Mounts a **named volume** (`mysql_data`, declared at the bottom of the file) onto
  `/var/lib/mysql` inside the container — that's where MySQL stores its actual database files.
  Without this, deleting the container = deleting the entire database. With it, the container
  can be deleted and recreated and the data is still there.

```yaml
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      timeout: 20s
      retries: 10
```
- Docker repeatedly runs `mysqladmin ping` to check MySQL is actually ready to accept connections
  (not just that the container process started — MySQL takes a few seconds to initialize).

```yaml
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
```
- Unlike `mysql`, this service isn't a premade image — `build:` tells Compose "build an image
  from this Dockerfile." `context` = the folder sent to Docker as the build's working
  directory (everything `COPY` in the Dockerfile is relative to this).

```yaml
    depends_on:
      mysql:
        condition: service_healthy
```
- "Don't start the backend until MySQL's healthcheck reports healthy." Plain `depends_on: [mysql]`
  (no condition) only waits for the container to *start*, not to be *ready* — a classic bug source
  where the app tries to connect to a DB that's still booting.

```yaml
    user: root
    command: >
      sh -c "
        mkdir -p /app/uploads/images &&
        chown -R spring:spring /app/uploads &&
        chmod -R 775 /app/uploads &&
        exec java ...
      "
```
- Overrides the Dockerfile's default `CMD`. Temporarily runs **as root** just long enough to fix
  file permissions on a mounted uploads folder (bind-mounted folders can have permission
  mismatches with the container's own user), then `exec`s into the actual Java process. `exec`
  matters here — it replaces the shell process with Java instead of running Java as a child
  process, so Java properly receives shutdown signals (`SIGTERM`) from Docker.

```yaml
    restart: unless-stopped
```
- Restart policy. Options: `no` (default), `always`, `on-failure`, `unless-stopped`.
  `unless-stopped` = "restart automatically if it crashes or the server reboots, *unless* a human
  explicitly ran `docker stop`." Good default for anything long-running.

```yaml
    networks:
      - app-network
```
- Puts this container on a custom network so it can reach other containers by service name (see
  [section 10](#10-networking--how-containers-talk-to-each-other)).

```yaml
  frontend:
    ...
    depends_on:
      - backend
```
- Notice: no `condition` here — just waits for the backend container to *start* (not be fully
  healthy). Simpler because the frontend can have its own retry logic for API calls.

```yaml
volumes:
  mysql_data:

networks:
  app-network:
    driver: bridge
```
- Declares the named volume and named network referenced above. `driver: bridge` = the default,
  standard "private virtual network on this one machine" mode (more in section 10).

### Production file's extra bits

```yaml
  frontend:
    expose:
      - "3000"
```
- `expose` (no host port) vs `ports` (`"host:container"`): `expose` just documents that other
  containers *on the same network* can reach port 3000 — it does **not** publish it to the host
  machine at all. The public internet never talks to the frontend container directly in this
  setup — only other containers can. That's intentional: a reverse proxy is the only thing
  exposed to the internet.

```yaml
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager:latest
    ports:
      - "80:80"
      - "443:443"
      - "81:81"
```
- This is the **reverse proxy** — the single public front door. It receives all real internet
  traffic on 80 (HTTP) and 443 (HTTPS), terminates SSL/TLS (handles the HTTPS certificate — via
  Let's Encrypt, hence a `letsencrypt` volume), and forwards requests internally to
  `frontend:3000`. Port 81 is its own admin dashboard for configuring those routing rules.
- **Why do this instead of exposing the frontend container's port directly?** One public entry
  point is easier to secure, easier to add HTTPS to once, and lets multiple apps be hosted behind
  the same IP by routing on hostname.

```yaml
    mem_limit: 2g
    memswap_limit: 2g
```
- Hard caps the container's RAM usage. Prevents one runaway container from starving the whole
  server. `memswap_limit` = the memory + swap ceiling combined (setting it equal to `mem_limit`
  effectively disables swap for that container).

---

## 6. Ports — what `3010:3000` actually means

The format is always:

```
"HOST_PORT:CONTAINER_PORT"
     ↑            ↑
  the host     inside the box
```

Left side = the port typed into a browser / Postman on the actual machine. Right side = the port
the **app itself** is listening on, inside its own sealed container. They don't have to match —
that's the whole point, it lets multiple containers all internally think they're on port 3000,
mapped to different host ports so they don't collide.

Example mappings, decoded:

| Mapping | Context | Meaning |
|---|---|---|
| `3390:3306` | dev compose | MySQL always listens on `3306` internally (its default). On the dev machine it's reached at `localhost:3390` instead of `3306` — likely so it doesn't clash with a MySQL that might already be running locally on the standard port. |
| `8090:8090` | dev + prod compose | A Spring Boot backend listening on `8090` inside the container (set via the Dockerfile's `EXPOSE 8090` and the app's config), published to `localhost:8090` on the host too — same number both sides, no translation needed. |
| `3010:3000` | dev compose | Next.js *always* listens on `3000` inside its container (Next.js's default). On the dev machine, `localhost:3010` is opened, and Docker forwards that to the container's internal `3000`. |
| `3306:3306` | prod compose | MySQL in prod, standard port both sides. Often labeled "internal only" — meaning: don't expose this to the public internet in real production (only the server itself should reach it; a hardened setup would remove this `ports:` entry entirely and rely on the internal Docker network). |
| *(`expose: 3000`, no host port)* | prod compose | Frontend has **no host port at all** in prod — nothing on the internet, and nothing on the host machine, can reach `localhost:3000` directly. Only sibling containers on the network (i.e. the reverse proxy) can reach it, by calling `http://frontend:3000`. |
| `80:80` | prod compose | Reverse proxy — plain HTTP, the standard "no port number needed in a URL" port. |
| `443:443` | prod compose | Reverse proxy — HTTPS, the standard encrypted-web port. |
| `81:81` | prod compose | Reverse proxy's own admin UI — visited at `http://server-ip:81` to log in and configure proxy rules/certificates. |

**Interview one-liner:** *"Port mapping is `-p HOST:CONTAINER`. It's how traffic gets from the
outside world into an otherwise-isolated container. If a port isn't published, nothing outside
Docker's internal network can reach that container at all — that's a feature, not a bug, for
things like databases and internal frontends that should stay private."*

---

## 7. Docker CLI reference (cleanup, volumes, networks, login)

The everyday build/run/stop/remove commands already live in [section 3](#3-working-with-images-and-containers-run-list-stop-remove).
This section covers the rest: image management extras, deeper inspection, cleanup, and registry auth.

### More image commands

```bash
docker build -t myapp:latest -f Dockerfile.dev .   # Use a specific Dockerfile (not the default named "Dockerfile")
docker rmi myapp:latest                 # Remove (delete) an image
docker push myuser/myapp:latest         # Upload an image to a registry (must docker login first)
docker tag myapp:latest myapp:v2        # Give an existing image an additional tag/name
docker history myapp:latest             # Show the layers that make up an image and their sizes
```

### More container inspection commands

```bash
docker logs --tail 100 backend           # Only the last 100 lines of logs
docker exec backend env                  # Run a one-off command inside a running container without attaching a full shell
docker inspect backend                   # Dump full JSON details about a container (its IP, mounts, env vars, config, everything)
docker stats                             # Live CPU/memory/network usage per running container, like `top` but for Docker
docker top backend                       # Show the actual OS processes running inside a container
docker cp backend:/app/logs ./logs       # Copy a file/folder OUT of a container to the host (or reverse the arguments to copy in)
```

### Cleanup (very commonly asked about)

```bash
docker system df                         # See how much disk space images/containers/volumes are using
docker system prune                      # Remove ALL stopped containers, unused networks, dangling images, and build cache
docker system prune -a                   # Same, but also removes any image not currently used by a running container (more aggressive)
docker container prune                   # Remove only stopped containers
docker image prune                       # Remove only dangling (untagged) images
docker volume prune                      # Remove only volumes not used by any container — ⚠️ this deletes data permanently
docker network prune                     # Remove unused networks
```

### Volumes & networks (standalone, outside compose)

```bash
docker volume create mydata              # Create a named volume manually
docker volume ls                         # List volumes
docker volume inspect mydata              # See where it lives on disk, which containers use it
docker volume rm mydata                  # Delete a volume (⚠️ data loss)

docker network create mynet              # Create a custom network
docker network ls                        # List networks
docker network inspect mynet             # See which containers are attached, their IPs
```

### Login / registry auth

```bash
docker login                             # Authenticate to Docker Hub (or -u/-p flags, or a private registry URL)
docker logout
```

---

## 8. All the docker-compose CLI commands needed

Startup/shutdown shell scripts in a project are typically just wrappers around these commands —
learning these commands is the same as understanding those scripts.

> Note: modern Docker ships `docker compose` (a subcommand, no hyphen) built in. Older setups use
> the standalone `docker-compose` (with a hyphen) — both work the same way, just installed
> differently. If one doesn't exist on a machine, try the other.

```bash
docker-compose -f docker-compose.dev.yml up -d
```
- Starts every service defined in that file. `-d` = detached, runs in the background instead of
  tying up the terminal streaming logs. Without `-f <file>`, Compose looks for a file literally
  named `docker-compose.yml` — if a project doesn't have one, `-f` is required every time.

```bash
docker-compose -f docker-compose.dev.yml up -d --build
```
- Same as above, but **force a rebuild** of any service with a `build:` key before starting it.
  Use this after source code changed and the new code needs baking into the image.

```bash
docker-compose -f docker-compose.dev.yml build backend
```
- Build (or rebuild) just the `backend` service's image, without starting anything.

```bash
docker-compose -f docker-compose.dev.yml down
```
- Stops and **removes** all containers (and the default network) for this compose file. Named
  volumes (like a MySQL data volume) are kept by default — safe to run repeatedly.

```bash
docker-compose -f docker-compose.dev.yml down -v
```
- Same, but also deletes the named volumes — ⚠️ this wipes the database data. Only do this on
  purpose (e.g. "start with a totally fresh database").

```bash
docker-compose -f docker-compose.prod.yml ps
```
- Show status of every service defined in this file (running, healthy, restarting, etc.) — like
  `docker ps` but scoped to just this compose project's containers.

```bash
docker-compose -f docker-compose.prod.yml logs backend
docker-compose -f docker-compose.prod.yml logs -f backend
```
- Same idea as `docker logs`, just addressed by service name instead of container name/ID, and
  automatically scoped to this compose project.

```bash
docker-compose -f docker-compose.prod.yml restart backend
```
- Restart just one service, leave the others running untouched.

```bash
docker-compose -f docker-compose.prod.yml up -d --no-deps backend
```
- Restart/recreate just `backend`, and **don't** also touch the services it `depends_on` (by
  default, `up` would also consider starting/recreating dependencies — `--no-deps` says "just this
  one"). A common pattern for a quick single-service rebuild without disturbing everything else.

```bash
docker-compose -f docker-compose.prod.yml exec backend sh
```
- Same as `docker exec`, but the *service* is named, and Compose figures out the running
  container for it.

```bash
docker-compose -f docker-compose.prod.yml config
```
- Validates the YAML and prints the fully-resolved config (with env vars substituted in) — great
  for debugging "why isn't this environment variable applying" issues.

```bash
docker-compose -f docker-compose.prod.yml pull
```
- For any service using `image:` (like a `mysql` or reverse-proxy service), pulls the latest
  version from the registry without touching services that use `build:`.

---

## 9. Volumes vs Bind Mounts (this trips everyone up)

Both let a container read/write files that live outside its own throwaway filesystem, but they're
different mechanisms:

- **Named volume** — e.g. `mysql_data:/var/lib/mysql`. Docker manages *where* this actually
  lives on the host disk (no need to know or care). Portable, works the same on any OS,
  the standard choice for **database data**.

- **Bind mount** — e.g. `./uploads:/app/uploads`. A specific host path is given explicitly.
  It's a direct window into a real folder on the machine. Great for **things a human wants to
  see/edit directly** — an uploaded images folder, or mounting live source code into a dev
  container for hot-reload.

**One-line interview answer:** *"A named volume is Docker-managed storage, portable and opaque —
best for data like databases. A bind mount points at a specific folder on the host that's
directly controlled — best for config files, uploads, or live-reloading source code in dev."*

---

## 10. Networking — how containers talk to each other

Every service in a compose file typically joins the same custom bridge network (e.g.
`app-network`). Docker gives every service a **DNS entry matching its service name** on that
network. That's the whole trick behind `http://backend:8090` inside a frontend's environment
variables — `backend` isn't a real hostname on the internet, it only resolves *inside* that
Docker network, to whichever container is running the `backend` service right now.

**The three network drivers worth knowing:**

| Driver | What it's for |
|---|---|
| `bridge` | Default. A private virtual network on one machine. Containers on it can reach each other by service/container name; the outside world can only reach published ports. Most Compose setups use this. |
| `host` | Container shares the host machine's network stack directly — no isolation, no port mapping needed (and none possible). Faster, but less safe and only works on Linux hosts natively. |
| `none` | No networking at all. Fully isolated. Rare — used for pure batch/compute jobs that shouldn't talk to anything. |

**Why a public API URL and an internal API URL are often different:** a browser (running outside
Docker entirely) can't resolve `backend` — that name only means something inside Docker's
network. So the browser needs the real `http://localhost:8090` (or a public domain). But when a
Next.js server itself (running inside a container) makes server-side API calls, it's *on* the
Docker network, so it can and should use the faster, internal `http://backend:8090`. This is a
genuinely common real-world gotcha, not just a training exercise.

---

## 11. Multi-stage builds — why Dockerfiles have "AS builder"

Without multi-stage builds, a final image would contain: the JDK/Gradle toolchain (or
npm + all dev dependencies), the full source code, intermediate build artifacts, AND the final
compiled output — even though only that last part is needed to *run* the app. That could
easily be 3-5x larger than necessary, and it means shipping source code and build tools to
production, which is both wasteful and an unnecessary security surface.

**Multi-stage build = multiple `FROM` lines in one Dockerfile.** Each `FROM` starts a fresh,
independent stage. Later stages can selectively `COPY --from=<stage name>` just the files they
need from an earlier stage. Only the **last** stage becomes the final image — everything from
earlier stages that isn't explicitly copied out gets thrown away.

Example: a `builder` stage has the full JDK + Gradle + source code, and compiles a jar. The final
stage starts fresh from a JRE-only base and copies in just `app.jar`. Result: a final image with
Java runtime + one jar file. No compiler, no Gradle, no source.

**Interview one-liner:** *"Multi-stage builds let you use a heavy image full of build tools to
compile the app, then copy only the compiled output into a small, clean runtime image — so the
shipped image doesn't carry compilers, source code, or dev dependencies."*

---

## 12. Extra concepts interviewers ask about

- **`ENTRYPOINT` vs `CMD`** — Both define what runs when the container starts. `CMD` is the
  *default* command and is easily overridden (e.g. by compose's `command:`). `ENTRYPOINT` is
  meant to be the fixed, "this container's whole purpose" executable, harder to override — often
  used with `CMD` supplying default *arguments* to the `ENTRYPOINT`.

- **`ARG` vs `ENV`** — `ARG` is a build-time-only variable (usable inside the Dockerfile during
  `docker build`, e.g. to pick a version number), gone once the image exists. `ENV` persists into
  the running container and is visible to the app at runtime.

- **Default vs custom bridge network** — if a custom network isn't declared in compose,
  Docker still creates one default bridge network per compose project automatically. Naming one
  explicitly (e.g. `app-network`) is mostly for clarity.

- **Docker Swarm / Kubernetes (orchestration)** — Compose is great for one machine. Once
  containers need to run across *many* machines, auto-scale, self-heal (restart failed containers
  on a different node), and do rolling zero-downtime deployments, that's when an **orchestrator**
  comes in. Docker Swarm is Docker's own built-in (simpler, less popular now); **Kubernetes (K8s)**
  is the industry standard (more complex, far more powerful — has its own vocabulary: Pods,
  Deployments, Services, ConfigMaps, Secrets, Ingress).

- **Secrets management** — plain `.env` files loaded via `env_file:` are fine for small setups,
  but real production systems often use dedicated secrets managers (Docker Secrets in Swarm,
  Kubernetes Secrets, AWS Secrets Manager, HashiCorp Vault) so passwords aren't sitting in a
  plaintext file on disk.

- **`docker save` / `docker load`** — export an image to a `.tar` file and import it elsewhere,
  without going through a registry at all. Useful for air-gapped environments.

- **`HEALTHCHECK` in a Dockerfile vs compose's `healthcheck:`** — either place can define one;
  compose's version overrides/duplicates the Dockerfile's own `HEALTHCHECK` if both exist. Worth
  knowing the difference between a weak "the process exists" check (e.g. `node --version`) and a
  real "the app actually responds correctly" check (e.g. an HTTP call to `/api/health`).

- **Image layer caching order matters** — a classic gotcha question: "why put `COPY package.json`
  before `COPY . .`?" Answer: Docker caches each layer; if `package.json` hasn't changed, Docker
  reuses the cached `npm ci` layer instead of re-running it, even if the source code changed.
  Copying everything at once in one `COPY . .` would invalidate that cache on every single code
  change, however small.

- **`docker-compose.override.yml`** — Compose automatically merges a file with this exact name on
  top of `docker-compose.yml` if both are present, letting a base file keep prod-safe defaults and
  a local override file adjust just a few values. An alternative pattern is keeping **entirely
  separate files** (e.g. `docker-compose.dev.yml`, `docker-compose.prod.yml`) instead of an
  override file — both are valid, worth comparing if asked.

- **Rootless Docker / Docker daemon security** — by default the Docker daemon runs as root on the
  host, which is itself a security consideration many companies address with rootless Docker mode.

---

## 13. Quick interview cheat-sheet (rapid fire Q&A)

**Q: Image vs container?**
A: Image = blueprint (read-only). Container = a running instance of that blueprint.

**Q: Why does data disappear after `docker rm`?**
A: Anything written to the container's own writable layer is deleted with the container. Only
data in a **volume** or **bind mount** survives.

**Q: What does `EXPOSE` actually do?**
A: Documents the port the app listens on inside the container. Does NOT publish it to the host —
that only happens with `-p`/`ports:`.

**Q: `docker-compose up` vs `docker-compose up -d`?**
A: Without `-d`, it runs in the foreground and streams logs to the terminal (Ctrl+C stops
everything). `-d` runs detached, in the background.

**Q: How do containers find each other by name?**
A: Docker's embedded DNS on a custom (or default) bridge network resolves service/container names
to internal IPs automatically.

**Q: Why use multi-stage builds?**
A: Smaller, more secure final images — ship only compiled output, not compilers/source/dev deps.

**Q: What's the difference between `docker stop` and `docker kill`?**
A: `stop` sends `SIGTERM` (graceful shutdown, app gets a chance to clean up), waits a grace period
(default 10s), then sends `SIGKILL` if it hasn't exited. `kill` sends `SIGKILL` immediately — no
grace period, no cleanup.

**Q: What happens on `docker system prune -a`?**
A: Deletes every stopped container, every image not used by a running container, unused networks,
and build cache. Does NOT touch volumes unless `--volumes` is added.

**Q: Bridge vs host network?**
A: Bridge = isolated virtual network, requires explicit port publishing. Host = container shares
the host's actual network stack directly, no isolation, no mapping needed.

**Q: Why run as a non-root user inside a container?**
A: Defense in depth — if the app is compromised, the attacker doesn't automatically get root
inside the container (and by extension, an easier path to escalate on the host).
