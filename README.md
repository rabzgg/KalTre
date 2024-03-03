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

# Trendz Analytics Installation Guide for Windows

## Prerequisites
This guide describes how to install Trendz Analytics on a Windows machine. Instructions are provided for Windows 10/8.1/8/7 32-bit/64-bit.

Hardware requirements depend on the amount of analyzed data and the number of devices connected to the system. To run Trendz Analytics on a single machine, you will need at least 1Gb of free RAM.

In small and medium installations, Trendz can be installed on the same server as ThingsBoard.

## Step 1. Install Java 11 (OpenJDK)
ThingsBoard service is running on Java 11. Follow these instructions to install OpenJDK 11.

1. Visit the Open JDK Download Page to download the latest OpenJDK 11 (LTS) MSI package.
2. Run the downloaded MSI package and follow the instructions. Make sure you have selected “Add to PATH” and “Set JAVA_HOME variable” options to “Will be installed on local hard drive” state.
3. Visit the PostgreSQL JDBC Download Page to download the PostgreSQL JDBC Driver.
4. Copy the downloaded file to `C:\Program Files\AdoptOpenJDK\jdk-11.0.10.9-hotspot\jre\lib\ext` and add a global variable named `CLASSPATH` with the value `.;C:\Program Files\AdoptOpenJDK\jdk-11.0.10.9-hotspot\jre\lib\ext\postgresql-42.2.18.jar` to your system.

You can check the installation using the following command:

    bash
    java -version

Expected command output is:

    openjdk version "11.0.xx"
    OpenJDK Runtime Environment (AdoptOpenJDK)(...)
    OpenJDK 64-Bit Server VM (AdoptOpenJDK)(...)

## Step 2. Trendz Analytics service installation 
Download and extract the package from the provided link. We assume you have extracted the Trendz package to the default location: C:\Program Files (x86)\trendz.

## Step 3. Obtain and configure license key
Once you have the license secret, you should put it in the Trendz configuration file located at C:\Program Files (x86)\trendz\conf\trendz.yml and insert your license secret in the license section.

    license:
    secret: "YOUR_LICENSE_SECRET_HERE" # license secret obtained from ThingsBoard License Portal

## Step 4. Configure connection with ThingsBoard Platform
Add the ThingsBoard REST API URL to the Trendz configuration file:
    tb.api.url: "http://localhost:8080"

## Step 5. Configure Trendz database
Trendz uses PostgreSQL as a database. You may install PostgreSQL on the same server as Trendz or use a managed service.

### PostgreSQL Installation
Download and install PostgreSQL 12.17 or newer.

### Create Database for Trendz
Using pgAdmin, create a database named trendz with postgres as the owner.

### Configure database connection for Trendz
Edit the datasource block in the Trendz configuration file:

    datasource:
     driverClassName: "org.postgresql.Driver"
     url: "jdbc:postgresql://localhost:5432/trendz"
     username: "postgres"
     password: "postgres"
     hikari:
      maximumPoolSize: 5

## Step 6. Run installation script
As an Administrator, run the `install.bat` script from the Trendz installation directory to install Trendz as a Windows service.

## Step 7. Start Trendz service
### To start the Trendz service, execute the following command:
    net start trendz
### To restart the service:
    net stop trendz && net start trendz
### Access the Trendz Web UI:
    http://localhost:8888/trendz

## Authentication
For the first authentication, use Tenant Administrator credentials from your ThingsBoard. Trendz uses ThingsBoard as an authentication service.

Ensure you replace placeholders such as YOUR_LICENSE_SECRET_HERE with actual values.
