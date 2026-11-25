# Dockerized Flask Application with GitHub Actions CI

## 1. Project Overview

This project demonstrates how to containerize a Python Flask application using Docker and automate Docker image building using GitHub Actions.

The application provides two endpoints:
- `/` – Displays a welcome message.
- `/health` – Returns the application health status in JSON format.

## 2. Technology Stack

- Python 3.12
- Flask
- Docker
- Git and GitHub
- GitHub Actions

## 3. Project Structure

```text
docker-flask/
├── .github/
│   └── workflows/
│       └── build.yml
├── .gitignore
├── Dockerfile
├── app.py
├── requirements.txt
└── README.md
```

## 4. Application Endpoints

| Endpoint | Method | Description |
|---|---|---|
| `/` | GET | Returns Hello from DevOps! |
| `/health` | GET | Returns application health status |

Expected responses:

**Homepage**
```text
Hello from DevOps!
```

**Health endpoint**
```json
{"status": "ok"}
```

## 5. Docker Configuration

The Dockerfile uses the official Python 3.12 slim image.

It performs the following operations:

1. Selects the Python base image.
2. Creates `/app` as the working directory.
3. Copies the requirements file.
4. Installs Flask.
5. Copies the application source code.
6. Exposes port 5000.
7. Starts the Flask application.

## 6. Build the Docker Image

Run:

```bash
docker build -t devops-flask-app .
```

## 7. Run the Docker Container

Run:

```bash
docker run -d --name devops-flask-container -p 5000:5000 devops-flask-app
```

Check the running container:

```bash
docker ps
```

## 8. Test the Application

Test the homepage:

```bash
curl http://localhost:5000/
```

Expected output:

```text
Hello from DevOps!
```

Test the health endpoint:

```bash
curl http://localhost:5000/health
```

Expected output:

```json
{"status": "ok"}
```

Both endpoints were tested successfully and returned HTTP 200 OK.

## 9. GitHub Actions – Continuous Integration

The project uses GitHub Actions to automatically build the Docker image whenever code is pushed to the repository.

Workflow location:

`.github/workflows/build.yml`

Workflow process:

1. GitHub detects a push event.
2. A GitHub-hosted Ubuntu runner starts.
3. The repository is checked out.
4. Docker builds the application image.

The workflow uses:

```yaml
name: Docker Build CI

on:
  push:

jobs:
  docker-build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t devops-flask-app .
```

**CI Result:** The Docker Build CI workflow executed successfully on GitHub Actions.

The workflow builds the image but does not push it to a container registry.

## 10. Debugging and Troubleshooting

The supplied Flask application already uses `0.0.0.0` and port `5000`. The application worked successfully in the local Docker environment.

The following are common reasons a containerized Flask application may not be reachable:

### Problem 1: Missing port mapping

**Cause:** The container is started without publishing its application port.

**Incorrect:**

```bash
docker run -d devops-flask-app
```

**Fix:**

```bash
docker run -d -p 5000:5000 devops-flask-app
```

### Problem 2: Incorrect host binding

**Cause:** Flask listens only on `127.0.0.1` inside the container.

**Incorrect:**

```python
app.run(host="127.0.0.1", port=5000)
```

**Fix:**

```python
app.run(host="0.0.0.0", port=5000)
```

### Problem 3: Missing Flask dependency

**Cause:** Flask is not installed inside the Docker image.

**Fix:** Include Flask in `requirements.txt` and install dependencies in the Dockerfile:

```dockerfile
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
```

### Problem 4: Incorrect startup command

**Cause:** The Dockerfile uses an incorrect application startup command.

**Fix:**

```dockerfile
CMD ["python", "app.py"]
```

### Problem 5: Container exits unexpectedly

**Cause:** The application may encounter an error during startup.

**Diagnosis:**

```bash
docker ps -a
docker logs devops-flask-container
```

**Fix:** Inspect the logs, correct the application or configuration error, rebuild the image, and restart the container.

## 11. Repository

GitHub Repository:

https://github.com/ASEEMNIHAL1/docker-flask

## 12. Conclusion

This project demonstrates the fundamentals of Docker containerization and Continuous Integration using GitHub Actions. The Flask application was successfully packaged into a Docker image, executed inside a container, and tested through its HTTP endpoints. GitHub Actions was configured to automate Docker image builds whenever changes are pushed to the repository.
