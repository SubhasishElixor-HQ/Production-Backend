# Docker Commands Cheat Sheet

A practical guide to essential Docker commands for building images, running containers, mapping ports, debugging, and pushing images to Docker Hub.

## 1. Prerequisites

- Docker Desktop installed and running
- A project containing a `Dockerfile`
- A Docker Hub account for publishing images

Verify your installation:

```powershell
docker --version
docker info
```

## 2. Basic Docker Commands

| Command | Description |
|---|---|
| `docker --version` | Display Docker version |
| `docker info` | Display Docker system information |
| `docker pull nginx` | Download an image |
| `docker images` | List local images |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker logs <container>` | Display container logs |
| `docker stop <container>` | Stop a running container |
| `docker start <container>` | Start a stopped container |
| `docker rm <container>` | Remove a container |
| `docker rmi <image>` | Remove an image |

## 3. Build a Docker Image

Run these commands from the directory containing your `Dockerfile`.

```powershell
# Build an image
docker build -t subhasishomnify/test-image .

# Build with a version tag
docker build -t subhasishomnify/test-image:v1.0 .

# List local images
docker images
```

**Important:** The final `.` specifies the current directory as the Docker build context. Without it, Docker reports that a required argument is missing.

## 4. Run a Container

```powershell
# Run a container in the background
docker run -d --name backend subhasishomnify/test-image

# Run with port mapping
docker run -d --name backend -p 8000:8000 subhasishomnify/test-image

# List running containers
docker ps

# View logs
docker logs backend

# Follow logs in real time
docker logs -f backend

# Stop the container
docker stop backend

# Start it again
docker start backend

# Remove the container
docker rm backend
```

## 5. Docker Port Mapping

Port mapping connects a port on your computer to a port inside a container.

**Syntax:**

```text
-p HOST_PORT:CONTAINER_PORT
```

Example:

```powershell
docker run -d --name backend -p 8000:8000 subhasishomnify/test-image
```

| Host port | Container port | Purpose |
|---|---|---|
| `8000` | `8000` | FastAPI application |
| `8080` | `80` | Web server |
| `5433` | `5432` | PostgreSQL |
| `6380` | `6379` | Redis |

To inspect published ports:

```powershell
docker ps
docker port backend
```

For a FastAPI application, ensure the application listens on `0.0.0.0` inside the container.

Example Dockerfile command:

```dockerfile
CMD ["uvicorn", "src.app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Replace `src.app.main:app` with your application's actual import path.

## 6. Push an Image to Docker Hub

Docker Hub stores and distributes container images.

### Step 1: Log in

```powershell
docker login
```

### Step 2: Build the image

```powershell
docker build -t subhasishomnify/test-image:latest .
```

### Step 3: Push the image

```powershell
docker push subhasishomnify/test-image:latest
```

### Step 4: Pull the image

```powershell
docker pull subhasishomnify/test-image:latest
```

### Step 5: Run the published image

```powershell
docker run -d --name backend -p 8000:8000 subhasishomnify/test-image:latest
```

**Note:** Replace `subhasishomnify` with your actual Docker Hub username. Create the `test-image` repository on Docker Hub if it does not already exist.

## 7. Tag and Push a Versioned Image

Version tags help identify specific releases.

```powershell
# Tag an existing image
docker tag subhasishomnify/test-image:latest subhasishomnify/test-image:v1.0

# Push the versioned image
docker push subhasishomnify/test-image:v1.0
```

Use meaningful tags such as `v1.0`, `v1.1`, or a Git commit SHA. Avoid relying exclusively on `latest` for production deployments.

## 8. Debugging Commands

```powershell
# Inspect a container
docker inspect backend

# Display the latest 100 log lines
docker logs --tail 100 backend

# Execute a shell inside a running container
docker exec -it backend sh

# Check resource usage
docker stats

# Check Docker disk usage
docker system df
```

Remove unused Docker resources carefully:

```powershell
docker system prune
```

This command removes unused containers, networks, dangling images and build cache. Review its confirmation prompt before proceeding.

## 9. Push Source Code to GitHub

GitHub stores your source code and project documentation, while Docker Hub stores built container images.

```powershell
# Initialize Git if necessary
git init

# Stage files
git add .

# Commit changes
git commit -m "Add Docker configuration and documentation"

# Set the main branch
git branch -M main

# Connect to your GitHub repository
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git

# Push source code
git push -u origin main
```

If Git is already initialized, or the remote already exists, skip the corresponding initialization or remote command.

## 10. Recommended Workflow

Follow this sequence when releasing a Dockerized application:

1. Write and validate the Dockerfile.
2. Build the image locally.
3. Run the container and test the application.
4. Check logs, ports and health.
5. Tag the image with a release version.
6. Authenticate with Docker Hub.
7. Push the image to Docker Hub.
8. Pull and run the image in the target environment.
9. Push the source code and documentation to GitHub.

## 11. Security Best Practices

- Never commit `.env` files, API keys, passwords or private certificates.
- Maintain a `.dockerignore` file to exclude unnecessary files and secrets from the build context.
- Use non-root users inside containers where practical.
- Prefer pinned dependency versions and trusted base images.
- Do not publish database ports publicly unless necessary.
- Use HTTPS and appropriate authentication for deployed services.
- Use specific image tags for reproducible releases.
- Back up persistent database data.

## References

- Docker documentation: https://docs.docker.com/
- Docker image build: https://docs.docker.com/build/
- Docker Hub: https://hub.docker.com/
- Docker Compose: https://docs.docker.com/compose/
