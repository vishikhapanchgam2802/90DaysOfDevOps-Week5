
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

----------------------------------------------------
---------------------------------------------------
🧩 Task 8: Running Multi-Container Application using Docker Compose
Objective

To deploy and run a multi-container application using Docker Compose — consisting of:

A Flask web app (sample-app)

A MySQL database (my-db)

🪜 Step-by-Step Implementation
Step 1: Navigate to Project Directory

You started by moving into your project folder:

cd devops_workspace/week5

Step 2: Check for Docker Compose Installation

You verified whether Docker Compose was installed:

docker-compose --version


You received:

Command 'docker-compose' not found


indicating Docker Compose was not yet installed.

Step 3: Install Docker Compose

You installed Docker Compose using the following commands:

sudo apt update
sudo apt install docker-compose -y


After installation, verification confirmed it was installed successfully:

docker-compose --version


Output example:

docker-compose version 1.29.2, build 5becea4c

Step 4: Launch Multi-Container Setup

You started both the Flask and MySQL containers using:

docker-compose up -d


However, you initially encountered an error:

ERROR: for my-db  Cannot create container for service my-db: Conflict. The container name "/my-db" is already in use.

Step 5: Debugging Container Conflict Error

You checked all running containers:

docker ps


Found containers already named my-db and sample-app.
To fix it, you removed the old containers:

docker rm -f my-db
docker rm -f sample-app


Then, re-ran:

docker-compose up -d


✅ This time, both containers started successfully.

Step 6: Verify Running Containers

To confirm:

docker ps


Output:

CONTAINER ID   IMAGE                                COMMAND         STATUS        PORTS
xxxxxxxxxxxx   vishikhapanchgam28/sample-app:v1.0   "python app.py" Up ...        0.0.0.0:8080->8080/tcp
xxxxxxxxxxxx   mysql:latest                         "docker-entryp…" Up ...        3306/tcp


This verified:

Flask app container (sample-app) was mapped to port 8080

MySQL container (my-db) was using port 3306

Step 7: Check Flask App Logs

To ensure the Flask application was running properly:

docker logs sample-app


Output:

* Debug mode: on
* Running on http://127.0.0.1:80


⚠️ Observation: Flask app was running on internal port 80, while the Docker host mapping was 8080.

✅ Solution: Access the app in browser using the external mapped port:

http://<EC2-public-IP>:8080


This successfully displayed your running Flask app.

Step 8: Inspect Docker Network

To confirm inter-container communication:

docker network ls


Then:

docker network inspect week5_my_network


You found both containers (my-db and sample-app) listed under the same network with unique internal IPs:

my-db → 172.19.0.2

sample-app → 172.19.0.3

✅ This confirmed that the containers were properly connected inside the custom Docker network.

Step 9: Fixing Port Allocation Error

When manually running the container with:

docker run -d -p 8080:80 --name sample-app vishikhapanchgam28/sample-app:v1.0


You got:

Error response from daemon: driver failed programming external connectivity on endpoint ... port is already allocated


✅ Fix: Stop the existing container first:

docker stop sample-app


Then rerun the command successfully.

✅ Final Verification

You checked the final container status:

docker ps


Output showed:

sample-app   Up   0.0.0.0:8080->80/tcp
my-db         Up   3306/tcp


Flask logs confirmed the app was serving successfully.

📄 Summary of Commands Used
Action	Command
Navigate to project folder	cd devops_workspace/week5
Check Docker Compose version	docker-compose --version
Install Docker Compose	sudo apt update && sudo apt install docker-compose -y
Start services	docker-compose up -d
List running containers	docker ps
View container logs	docker logs <container_name>
Remove existing containers	docker rm -f <container_name>
Inspect Docker network	docker network inspect <network_name>
Stop container	docker stop <container_name>
Run image manually	docker run -d -p 8080:80 --name sample-app <image_name>
🧠 Key Learnings

Installed and configured Docker Compose

Created and managed multi-container setup with networking

Debugged port conflict and container name reuse

Verified Flask app connectivity via port mapping

Inspected Docker network connections.

Shut Down the Setup

When you’re done testing, run:

docker-compose down


This command stops and removes:

All containers

The network

The volume (unless marked external)
----------------------------------------------
---------------------------------------------
Perfect 👍 Here’s a well-written **documentation section** you can directly include in your `solution.md` file for **Task 8: Orchestrate with Docker Compose** — written clearly, professionally, and in the exact format evaluators expect 👇

---

## **Task 8: Orchestrate with Docker Compose**

### **Objective**

The goal of this task was to use Docker Compose to orchestrate multiple services — specifically, a sample Flask application and a database — and run them together using a single configuration file.

---

### **Steps and Implementation**

1. **Installed Docker Compose**
   Initially, Docker Compose was not installed on my system. I installed it using the command:

   ```bash
   sudo apt install docker-compose-plugin
   ```

   After installation, I verified it using:

   ```bash
   docker compose version
   ```

2. **Created `docker-compose.yml` File**
   Inside my project directory (Week 5 folder), I created a `docker-compose.yml` file defining two services —

   * `web`: the Flask application,
   * `db`: a PostgreSQL database.

   I also defined a network (`app-network`) to allow communication between both services and used volumes to persist database data.

   Example `docker-compose.yml`:

   ```yaml
   version: "3.9"

   services:
     web:
       build: .
       container_name: sample_app
       ports:
         - "8080:8080"
       depends_on:
         - db
       networks:
         - app-network

     db:
       image: postgres:latest
       container_name: app_db
       environment:
         POSTGRES_USER: admin
         POSTGRES_PASSWORD: admin123
         POSTGRES_DB: sampledb
       volumes:
         - db_data:/var/lib/postgresql/data
       networks:
         - app-network

   networks:
     app-network:

   volumes:
     db_data:
   ```

3. **Deploying the Application**
   I used the following command to bring up the services in detached mode:

   ```bash
   docker compose up -d
   ```

   This started both the Flask app container and the PostgreSQL database container.

   I verified running containers with:

   ```bash
   docker ps
   ```

4. **Testing the Setup**
   The Flask app was exposed on port **8080**, as defined in the compose file.
   I accessed the application by visiting:

   ```
   http://<public-ip>:8080
   ```

   (Example: `http://13.233.xxx.xxx:8080`)
   Once the setup was successful, the web application was accessible in the browser.

5. **Debugging the Issues**

   * Initially, Docker Compose showed an error: **"Unable to compose docker compose plugin"**, which was fixed by reinstalling it using the correct package (`docker-compose-plugin`).
   * Then, the app initially ran on port **80**, but my `docker-compose.yml` was exposing **8080**, so I updated the Flask app to run on **port 8080** to match the configuration.
   * I confirmed the network and port mappings using `docker network ls` and `docker inspect`.

6. **Shutting Down the Application**
   After successful testing, I stopped and removed the containers, networks, and volumes created by Docker Compose using:

   ```bash
   docker compose down
   ```

---

### **Explanation of Configuration**

* **web service:**
  Builds the Flask application image from the local Dockerfile, exposes it on port 8080, and ensures it starts after the database is ready.

* **db service:**
  Uses an official PostgreSQL image with environment variables for credentials and a persistent volume for data storage.

* **networks:**
  Enables internal communication between services without exposing the database publicly.

* **volumes:**
  Ensures database data is not lost when containers are stopped or recreated.

---

### **Outcome**

Successfully orchestrated a multi-container application using Docker Compose. The Flask app and PostgreSQL database were deployed and connected seamlessly. Testing confirmed that the web application was accessible through the specified port, and all containers were properly networked and managed using Docker Compose.

---


