# Containerized Flask Application

A Flask web application containerized using Docker and deployed on Kubernetes using a local kind cluster.

## Project Overview

This project demonstrates a basic DevOps workflow:

**Flask Application → Docker → Kubernetes → GitHub**

The application is containerized using Docker and deployed to a Kubernetes cluster with two replicas. A Kubernetes Service exposes the application and provides stable networking to the Pods.

## Architecture

```text
                    User / Browser
                          |
                          v
              Kubernetes Service
             devops-flask-service
                          |
                          v
               Kubernetes Deployment
                          |
                 +--------+--------+
                 |                 |
                 v                 v
              Pod 1              Pod 2
                 |                 |
                 v                 v
            Flask App         Flask App
                 |                 |
                 +--------+--------+
                          |
                    Docker Image
                devops-flask-app:v1
```

## Technologies Used

- Python
- Flask
- Docker
- Kubernetes
- kind
- kubectl
- Git
- GitHub

## Project Structure

```text
Containerized-Flask-Application/
├── .dockerignore
├── .gitignore
├── Dockerfile
├── README.md
├── app/
│   ├── app.py
│   └── requirements.txt
└── k8s/
    ├── deployment.yaml
    └── service.yaml
```

## Application

The Flask application provides a simple HTTP endpoint:

```text
GET /
```

Response:

```text
DevOps application is running!
```

## Run the Application Locally

Activate the Python virtual environment:

```bash
source app/venv/bin/activate
```

Install the required dependencies:

```bash
pip install -r app/requirements.txt
```

Run the application:

```bash
python app/app.py
```

Open the application in a browser:

```text
http://localhost:5000
```

## Docker

### Build the Docker Image

```bash
docker build -t devops-flask-app:v1 .
```

### Run the Docker Container

```bash
docker run -d \
  --name devops-flask-container \
  -p 5000:5000 \
  devops-flask-app:v1
```

### Test the Container

```bash
curl http://localhost:5000
```

Expected response:

```text
DevOps application is running!
```

## Kubernetes Deployment

This project uses a local Kubernetes cluster created with kind.

### Load the Docker Image into kind

```bash
kind load docker-image devops-flask-app:v1 --name devops-lab
```

### Deploy the Application

```bash
kubectl apply -f k8s/deployment.yaml
```

### Create the Kubernetes Service

```bash
kubectl apply -f k8s/service.yaml
```

### Verify the Deployment

```bash
kubectl get deployments
```

```bash
kubectl get pods
```

```bash
kubectl get services
```

The Deployment runs two replicas of the Flask application.

## Access the Application

Port-forward the Kubernetes Service:

```bash
kubectl port-forward service/devops-flask-service 8080:5000
```

Open the following URL in a browser:

```text
http://localhost:8080
```

Expected response:

```text
DevOps application is running!
```

## Kubernetes Self-Healing

The application is deployed with two replicas.

```yaml
replicas: 2
```

If one Pod is deleted or fails, the Kubernetes Deployment controller automatically creates a replacement Pod to maintain the desired number of replicas.

This project was tested by manually deleting a running Pod and observing Kubernetes automatically create a replacement Pod.

## Kubernetes Service Discovery

The Kubernetes Service uses the following selector:

```yaml
selector:
  app: devops-flask
```

This allows the Service to route traffic to the Pods belonging to the application.

The Service exposes port `5000` and forwards traffic to the Flask containers running on port `5000`.

## DevOps Workflow

```text
Developer
    |
    v
Git Repository
    |
    v
GitHub
    |
    v
Docker Image
    |
    v
Kubernetes
    |
    v
Deployment
    |
    v
Pods
    |
    v
Service
    |
    v
Application
```

## Current Project Status

- [x] Flask application
- [x] Dockerfile
- [x] Docker image
- [x] Docker container
- [x] Kubernetes cluster
- [x] Kubernetes Deployment
- [x] Two Pod replicas
- [x] Kubernetes Service
- [x] Kubernetes self-healing test
- [x] Git repository
- [x] GitHub repository
- [ ] GitHub Actions CI/CD
- [ ] Container registry integration
- [ ] Automated Kubernetes deployment
- [ ] AWS deployment

## Future Improvements

The next phase of this project will introduce CI/CD using GitHub Actions.

The planned pipeline is:

```text
Git Push
   |
   v
GitHub Actions
   |
   +---- Run Tests
   |
   +---- Build Docker Image
   |
   +---- Push Image to Container Registry
   |
   +---- Deploy to Kubernetes
```

This will automate the build and deployment process.
