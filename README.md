# Trendz Installation on Windows using Docker

## Prerequisites

- Docker Desktop for Windows
- Docker Compose
- At least 2GB of RAM allocated to Docker

## Installation Steps

### 1. Download the Docker Compose File

- Create a directory for your Trendz installation.
- Inside this directory, create a file named `docker-compose.yml`.
- Copy the provided Docker Compose code into this file.

### 2. Configure Docker Compose File

- Ensure Docker Desktop is running and has sufficient resources allocated.
- Open the `docker-compose.yml` file in a text editor.
- Adjust environment variables if necessary to suit your setup.

### 3. Launch Trendz

- Open a command prompt or PowerShell window.
- Navigate to the directory where your `docker-compose.yml` file is located.
- Run the command: `docker-compose up -d`
- This command will download the necessary Docker images and start the Trendz service.

  ## Docker Compose File Content
  
  ```version: '3.0'
  services:
    mytrendz:
      restart: always
      image: "thingsboard/trendz:1.10.3-HF3"
      ports:
        - "8888:8888"
      environment:
        TB_API_URL: thingsboard.cloud
        TRENDZ_LICENSE_SECRET: <PUT_YOUR_LICENSE_SECRET_HERE>
        TRENDZ_LICENSE_INSTANCE_DATA_FILE: /data/license.data
        SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/trendz
        SPRING_DATASOURCE_USERNAME: postgres
        SPRING_DATASOURCE_PASSWORD: postgres
        SCRIPT_ENGINE_PROVIDER: DOCKER_CONTAINER
        SCRIPT_ENGINE_DOCKER_PROVIDER_URL: mypyexecutor:8080
        SCRIPT_ENGINE_TIMEOUT: 30000
      volumes:
        - mytrendz-data:/data
        - mytrendz-logs:/var/log/trendz
    mypyexecutor:
      restart: always
      image: "thingsboard/trendz-python-executor:1.10.3"
      ports:
        - "8080"
      environment:
        SCRIPT_ENGINE_RUNTIME_TIMEOUT: 30000
        EXECUTOR_MANAGER: 1
        EXECUTOR_SCRIPT_ENGINE: 6
        THROTTLING_QUEUE_CAPACITY: 10
        THROTTLING_THREAD_POOL_SIZE: 6
        NETWORK_BUFFER_SIZE: 10485760
    postgres:
      restart: always
      image: "postgres:15"
      ports:
        - "5432"
      environment:
        POSTGRES_DB: trendz
        POSTGRES_PASSWORD: postgres
      volumes:
        - mytrendz-data/db:/var/lib/postgresql/data
  volumes:
    mytrendz-data:
      external: true
    mytrendz-logs:
      external: true
    mytrendz-data-db:
      external: true

### 4. Verify Installation

- Once the containers are up, open your web browser.
- Navigate to `http://localhost:8080` to access the Trendz login page.
- The default login credentials are provided in the official documentation.

### 5. Stop Trendz

- To stop Trendz, run the command: `docker-compose down`
- This command stops and removes the containers created by Docker Compose.
