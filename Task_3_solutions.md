
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


Would you like me to now prepare the **Task 4 (Multi-stage Docker Build)** section implementation in the same well-documented format (with code, commands, and explanation ready for `solution.md`)?
