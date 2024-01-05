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

# Setting up cAdvisor and Promotheus  

cAdvisor (Container Advisor) provides container users an understanding of the resource usage and performance characteristics of their running containers. 
  
1. Run cAdvisor in a Docker Container: 

   docker run \ 

     --volume=/:/rootfs:ro \ 

     --volume=/var/run:/var/run:rw \ 

     --volume=/sys:/sys:ro \ 

     --volume=/var/lib/docker/:/var/lib/docker:ro \ 

     --publish=8080:8080 \ 

     --detach=true \ 

     --name=cadvisor \ 

     gcr.io/cadvisor/cadvisor:v0.39.3 


   This command runs cAdvisor on port 8080. You can access the cAdvisor web UI at `http://server_ip:8080`. 

 2: Install and Configure Prometheus 

Prometheus is an open-source monitoring system with a dimensional data model, flexible query language, efficient time series database, and modern alerting approach. 

1. Create a Prometheus Configuration File: 

   - Create a directory for Prometheus configuration: 

     mkdir /etc/prometheus 

   - Create a configuration file named `prometheus.yml` in this directory. 

   - Edit the file and add the following configuration: 

     ```yaml 

     global: 

       scrape_interval: 15s 

     scrape_configs: 

       - job_name: 'prometheus' 

         static_configs: 

           - targets: ['localhost:9090'] 

       - job_name: 'cadvisor' 

         scrape_interval: 5s 

         static_configs: 

           - targets: ['server_ip:8080'] 

   - Replace `server_ip` with your actual server IP. 

2. Run Prometheus in a Docker Container: 

   docker run \ 

     -p 9090:9090 \ 

     -v /etc/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml \ 

     --name prometheus \ 

     --detach \ 

     prom/prometheus 

   This command runs Prometheus on port 9090. You can access the Prometheus web UI at `http://server_ip:9090`.
   
4: Setting Up Alerts (Optional) 

If you want to set up alerts based on metrics, you'll need to configure Prometheus's Alertmanager. This involves additional setup and configuration. You can find detailed instructions in the [Prometheus Alerting documentation](https://prometheus.io/docs/alerting/latest/overview/). 

# To monitor various performance metrics of your Docker containers using cAdvisor and Prometheus, you can use a range of queries. These queries can help you assess different aspects such as CPU usage, memory consumption, network IO, and more. After setting up the queries in Prometheus, you can visualize this data using a tool like Grafana to create graphs and dashboards. 

Common Metrics to Monitor: 

1. CPU Usage: Measures the CPU resources used by a container. 

2. Memory Usage: Tracks the memory consumed by a container. 

3. Network I/O: Monitors the network traffic to and from a container. 

4. Disk I/O: Assesses the disk read/write operations by a container. 

5. Container Uptime: Duration for which a container has been running. 

CPU Usage: 

   rate(container_cpu_usage_seconds_total{name="container_name"}[1m]) 

   -> Replace `container_name` with your container's name. 

  

Memory Usage: 

   container_memory_usage_bytes{name="container_name"} 

Network Input: 

   rate(container_network_receive_bytes_total{name="container_name"}[1m]) 

Network Output: 

   rate(container_network_transmit_bytes_total{name="container_name"}[1m]) 

Disk Read: 

   rate(container_fs_reads_bytes_total{name="container_name"}[1m]) 

Disk Write: 

   rate(container_fs_writes_bytes_total{name="container_name"}[1m]) 

Uptime: 

  time() - container_start_time_seconds{name="container_name"} 

Note - Make sure to set the inbound and outbound traffic properly for your instance in AWS.
