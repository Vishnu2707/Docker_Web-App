# Deployment of Django Web App in Docker Containers

This guide outlines the steps taken to deploy a Django web application on an AWS Ubuntu server using Docker. The application is set up to run in three separate Docker containers, each mapped to a unique port.

-> Prerequisites

- AWS Ubuntu Server
- Docker installed on the server
- GitHub repository with Django application code

-> Step-by-Step Guide

1. Pulling the Application from GitHub

Clone the repository to your AWS Ubuntu server:

```bash
git clone https://github.com/YourGitHubUsername/YourRepoName.git
cd YourRepoName
```

2. Building the Docker Image

Create a Dockerfile in your project directory, then build the Docker image:

```bash
docker build -t django-app .
```

3. Running the Containers

Run three instances of the Docker image, each mapped to a different port:

```bash
# Run on port 8001
docker run -d -p 8001:8000 django-app

# Run on port 8002
docker run -d -p 8002:8000 django-app

# Run on port 8003
docker run -d -p 8003:8000 django-app
```

4. Verifying the Containers

Check that the containers are running correctly:

```bash
docker ps
```

5. Accessing the Application

The Django application should now be accessible at:

- `http://<your-server-ip>:8001`
- `http://<your-server-ip>:8002`
- `http://<your-server-ip>:8003`

6. Setting Up Auto Restart of Docker Containers

To ensure the containers restart automatically after a reboot, set the restart policy:

```bash
docker update --restart=unless-stopped <container_id>
```

Repeat this for each container ID.

Conclusion

The Django application is now successfully deployed in separate Docker containers on different ports, ensuring isolated environments for various purposes such as development, testing, and production.

Note - Make sure to set the inbound and outbound traffic properly for your instance in AWS.
