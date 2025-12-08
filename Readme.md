# Docker Learnings Pushed

![Docker Logo](https://www.docker.com/wp-content/uploads/2022/03/Moby-logo.png)

## 🐳 Welcome
This repository contains my notes, examples, and hands-on exercises as I learn Docker from scratch.  
Everything here is aimed at helping beginners understand containerization in a simple and practical way.

---

### ### Steps to Install Docker (Windows)

#### 1. Download Docker Desktop
- Visit the official Docker website.
- Download the latest **Docker Desktop for Windows (.exe)** installer.

#### 2. Install WSL 2 (Recommended)
- Enable **WSL 2** on Windows.
- Install a Linux distribution (e.g., Ubuntu).
- Ensure your WSL kernel is up to date.

#### 3. Run the Docker Desktop Installer
- Execute the downloaded `.exe` file.
- Follow the instructions to complete the setup.
- Docker Desktop will download required components.
- After installation, ensure Docker Engine is running successfully.

---

Notes are also added here to record
- Docker Concepts
- Commands Cheatsheet

## 🖥 Application Setup and Testing

### Application Code
You can find the Node.js application here:  
[Docker-Test-App Repository](https://github.com/Atharva-Deshmukh/Docker-Test-App)

---

### ARCHITECTURAL FLOW of the App

```text

┌─────────────────────────────────────────────────────────────┐
│                    Docker Container Setup                    │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │              Docker Network: mongoDB-net            │   │
│  │                                                     │   │
│  │   ┌──────────────┐         ┌──────────────┐        │   │
│  │   │   MongoDB    │         │ Mongo-Express│        │   │
│  │   │              │         │              │        │   │
│  │   │  Port: 27017 ├─────────► Port: 8081   │        │   │
│  │   │  User: admin │         │  Web UI Auth:│        │   │
│  │   │  Pass: qwerty│         │  admin/pass  │        │   │
│  │   └──────┬───────┘         └──────┬───────┘        │   │
│  └──────────┼─────────────────────────┼────────────────┘   │
│             │                         │                    │
│    ┌────────▼─────┐         ┌────────▼─────┐              │
│    │ Host:27017   │         │ Host:8081    │              │
│    │ (localhost)  │         │ (localhost)  │              │
│    └──────────────┘         └──────────────┘              │
│                                                             │
└─────────────────────────────────────────────────────────────┘

Flow: User → localhost:8081 → Mongo-Express → MongoDB
Credentials: admin/qwerty (MongoDB) + admin/pass (Web UI).

Mongo DB is running inside docker container, not in our local

```

### Step 1: Start MongoDB and Mongo-Express Containers

Start the MongoDB container:

```cmd
docker run -d ^
-p27017:27017 ^
--name mongo-container ^
--network mongoDB-net ^
-e MONGO_INITDB_ROOT_USERNAME=admin ^
-e MONGO_INITDB_ROOT_PASSWORD=qwerty ^
mongo
```

```cmd
docker run -d ^
-p 8081:8081 ^
--name mongo-express-container ^
--network mongoDB-net ^
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin ^
-e ME_CONFIG_MONGODB_ADMINPASSWORD=qwerty ^
-e ME_CONFIG_MONGODB_SERVER=mongo-container ^
-e ME_CONFIG_BASICAUTH=false ^
mongo-express
```

Note: Both containers should be run on the same Docker network (mongoDB-net) for proper communication.

Repo where app is there: https://github.com/Atharva-Deshmukh/Docker-Test-App

Start the app
```cmd
node server.js
```
Check if the app is working. Fetch all users in the database:
```cmd
http://localhost:5050/getUsers
```

To modify the database directly via the web UI, open Mongo-Express:

```cmd
http://localhost:8081/db/admin/
```

