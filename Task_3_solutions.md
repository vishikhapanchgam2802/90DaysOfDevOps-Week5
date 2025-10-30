
## 🧩 **Task 3: Explore Docker Terminologies and Components**

### 🧠 **Objective**

To understand and document important Docker terminologies and components that are essential for working with containerized applications.

--

### ⚙️ **Key Docker Terminologies**

| **Term**       | **Description**                                                                                                                                                                                                                   |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Image**      | A Docker image is a lightweight, standalone, and immutable file that contains everything needed to run an application—code, runtime, system tools, libraries, and dependencies. It serves as a blueprint for creating containers. |
| **Container**  | A container is a running instance of a Docker image. Containers are isolated environments that share the host system’s kernel but have their own filesystem, network, and process space.                                          |
| **Dockerfile** | A Dockerfile is a text document containing a series of instructions (like `FROM`, `COPY`, `RUN`, `CMD`, etc.) that define how to build a Docker image. It acts as a recipe for creating the image.                                |
| **Volume**     | Volumes are used to persist and share data between containers. Even if a container is deleted, the data inside a volume remains intact.                                                                                           |
| **Network**    | Docker networks enable communication between containers. Containers on the same network can interact with each other using their container names as hostnames.                                                                    |
| **Docker Hub** | Docker Hub is a public cloud-based registry where developers can store, share, and manage their Docker images.                                                                                                                    |
| **Registry**   | A registry is a storage and distribution system for Docker images (e.g., Docker Hub or a private registry).                                                                                                                       |

---

### 🧩 **Main Docker Components**

| **Component**                        | **Description**                                                                                                                               |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **Docker Engine**                    | The core part of Docker responsible for building and running containers. It includes the **Docker Daemon**, **REST API**, and **CLI**.        |
| **Docker Daemon (`dockerd`)**        | A background service that listens for Docker API requests and manages images, containers, networks, and volumes. It runs on the host machine. |
| **Docker Client (`docker` command)** | The command-line tool used by users to interact with the Docker Daemon. For example: `docker build`, `docker run`, `docker ps`.               |
| **Docker Images**                    | Read-only templates used to create containers. They are built using instructions defined in a Dockerfile.                                     |
| **Docker Containers**                | Executable instances of images that include all the dependencies and configurations needed to run an application.                             |
| **Docker Hub**                       | A cloud-based public registry where Docker users can publish and pull container images.                                                       |
| **Docker Compose**                   | A tool that allows you to define and manage multi-container applications using a single YAML file (`docker-compose.yml`).                     |
| **Docker Registry**                  | A repository where Docker images are stored and retrieved from (either public like Docker Hub or private).                                    |

---

### 🔗 **How Docker Components Work Together**

1. The **Docker Client** sends commands to the **Docker Daemon** (e.g., to build or run containers).
2. The **Docker Daemon** builds images based on the **Dockerfile** and stores them locally or pushes them to a **Registry** like Docker Hub.
3. When a container is run, the **Docker Engine** uses the stored image to create an isolated runtime environment (the container).
4. **Volumes** and **Networks** are managed by the Docker Engine to enable data persistence and inter-container communication.

---

### 💡 **Example Command References**

Here are a few Docker commands related to the components mentioned:

```bash
# List all running containers
docker ps

# List all Docker images
docker images

# Create a new Docker volume
docker volume create my_volume

# List Docker networks
docker network ls

# Inspect Docker system info
docker info
```

---

### 📝 **Observations**

* Docker’s modular structure (Engine, CLI, Daemon, Registry) allows efficient image management and deployment.
* Containers are faster and lighter than virtual machines because they share the host OS kernel.
* Docker volumes are essential for data persistence, and Docker networks enable scalable microservices communication.
-------------------------------------------------------------------------------------------------



## 📝 **solution.md — Task 4: Multi-Stage Docker Build**

### Task 4: Optimize Docker Image with Multi-Stage Builds

#### Folder Structure:

```
week5/
├── app/
│   └── app.py
├── Dockerfile                # Original Dockerfile from Task 2
├── Dockerfile.multistage     # Optimized multi-stage Dockerfile
└── solution.md
```

---

### Multi-Stage Dockerfile Content

```dockerfile
# ---------- Stage 1: Builder ----------
FROM python:3.10-slim AS builder
WORKDIR /app

# Copy app files into the container
COPY app/ /app/

# Install Flask in the builder stage
RUN pip install --no-cache-dir flask

# ---------- Stage 2: Final Image ----------
FROM python:3.10-slim
WORKDIR /app

# Copy application code from builder stage
COPY --from=builder /app /app

# Copy installed Python libraries from builder stage
COPY --from=builder /usr/local/lib/python3.10 /usr/local/lib/python3.10
COPY --from=builder /usr/local/bin /usr/local/bin

# Expose port 80 for access
EXPOSE 80

# Command to run the Flask app
CMD ["python", "app.py"]
```

---

### Explanation of Multi-Stage Dockerfile

1. **Stage 1 — Builder Stage**

   * `FROM python:3.10-slim AS builder` → Use a lightweight Python image for building.
   * `WORKDIR /app` → Set the working directory inside the container.
   * `COPY app/ /app/` → Copy your app code into the container.
   * `RUN pip install --no-cache-dir flask` → Install Flask **inside this stage only**.

   ✅ This stage includes all tools and dependencies needed to build the app.

2. **Stage 2 — Final Stage**

   * `FROM python:3.10-slim` → Start a **fresh, clean image** for the final app.
   * `COPY --from=builder /app /app` → Copy only your app code.
   * `COPY --from=builder /usr/local/lib/python3.10 /usr/local/lib/python3.10`
     `COPY --from=builder /usr/local/bin /usr/local/bin` → Copy installed libraries (like Flask) from the builder.
   * `EXPOSE 80` → Make port 80 available.
   * `CMD ["python", "app.py"]` → Run your Flask app.

   ✅ This ensures the final image is **smaller**, **cleaner**, and contains only what is necessary to run your app.

**Key Idea:** Multi-stage builds separate the **building environment** from the **final runtime environment**, reducing image size and vulnerabilities.

---

### Commands Used

1. **Build the multi-stage image**

```bash
docker build -f Dockerfile.multistage -t <your-username>/sample-app:multi-stage .
```

2. **Run the container**

```bash
docker run -d -p 8080:80 <your-username>/sample-app:multi-stage
```

3. **Check running containers**

```bash
docker ps
```

4. **View logs**

```bash
docker logs <container_id>
```

---

### Observations

| Image Type        | Size   |
| ----------------- | ------ |
| Original Image    | 142 MB |
| Multi-Stage Image | 53 MB  |

* The multi-stage image is **much smaller** because it only contains the final app and necessary libraries.
* All build tools and unnecessary files are removed.
* The app runs successfully both **on the server** and is accessible from a browser using the server’s public IP and mapped port.

---

### Benefits of Multi-Stage Builds

* ✅ Smaller image size → faster to download and deploy
* ✅ Cleaner environment → fewer vulnerabilities
* ✅ Easier to maintain → only necessary files copied
* ✅ Faster startup and pull times

--------------------------------------------------------------------
--------------------------------------------------------------------
🧩 Task 6: Persist Data with Docker Volumes
✅ Step 1: Create a Docker Volume
docker volume create my_volume

✅ Step 2: Run a Container with the Volume
docker run -d -v my_volume:/app/data vishikhapanchgam28/sample-app:v1.0

✅ Step 3: Verify Volume Creation
docker volume ls


You’ll see an entry like:

DRIVER    VOLUME NAME
local     my_volume

✅ Step 4: Explanation (For solution.md)

Docker volumes provide data persistence by storing data outside the container’s writable layer.
Even if a container is deleted, the data stored in the volume remains safe.
They are useful for:

Databases that store data permanently

Sharing data between containers

Backing up and restoring data easily
--------------------------------------------
----------------------------------------------
🌐 Task 7: Configure Docker Networking
✅ Step 1: Create a Custom Docker Network
docker network create my_network

✅ Step 2: Run Containers on the Same Network

Run your sample app container:

docker run -d --name sample-app --network my_network vishikhapanchgam28/sample-app:v1.0


Run a MySQL database container on the same network:

docker run -d --name my-db --network my_network -e MYSQL_ROOT_PASSWORD=root mysql:latest

✅ Step 3: Verify Network
docker network inspect my_network


You’ll see both containers (sample-app and my-db) connected to the same network.

✅ Step 4: Explanation (For solution.md)

Docker networking allows containers to communicate with each other over a virtual network.
When containers are on the same network:

They can communicate using container names as hostnames

No need to expose internal ports

Provides isolation and flexibility for multi-container apps
