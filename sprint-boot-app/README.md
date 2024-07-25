# PayMyBuddy Application

## Overview
The PayMyBuddy application is a Java-based application that uses MySQL as its backend database. This project uses Docker and Docker Compose to define and orchestrate the environment.

## Prerequisites
- Docker
- Docker Compose

## Architecture
- **paymybuddy**: Java application running on Amazon Corretto 17.
- **paymybuddydb**: MySQL database server.

## Repository Structure
- `Dockerfile`: Defines the Docker configuration for building the PayMyBuddy Java application image.
- `docker-compose.yml`: Orchestrates the setup of the PayMyBuddy application and its MySQL database.

## Configuration
- The Java application is configured through environment variables to connect to the MySQL database.
- The MySQL service is pre-configured with a root password, a database, and a user password. The initial schema and data are loaded from the `./initdb` directory.

## Building the Application
1. **Build the Docker Image for PayMyBuddy**
    ```bash
    docker build -t paymybuddy .
    ```
   This command builds the Docker image using the Dockerfile at the root of the project.

2. **Tag the Docker Image**
   Tag your Docker image to prepare it for the push to a Docker registry:
    ```bash
    docker tag paymybuddy yourusername/paymybuddy:latest
    ```
   Replace `yourusername` with your Docker Hub username.

3. **Push the Docker Image to Docker Hub**
   Before pushing the image, make sure you are logged into Docker Hub:
    ```bash
    docker login
    ```
   Then, push the image:
    ```bash
    docker push yourusername/paymybuddy:latest
    ```

2. **Start the Services**
    ```bash
    docker-compose up -d
    ```
   This command starts all services defined in `docker-compose.yml`. It builds the services if they are not already built.

## Usage
- **Accessing the Application**
  - The PayMyBuddy application is accessible at `http://localhost:8080` after starting the services.
- **Accessing the Database**
  - Connect to the MySQL database at `localhost:3306` using the username `root` and the password `password`.

## Environment Variables
- `SPRING_DATASOURCE_USERNAME`: Username for the database connection.
- `SPRING_DATASOURCE_PASSWORD`: Password for the database connection.
- `SPRING_DATASOURCE_URL`: JDBC URL for connecting to the MySQL database.

## Initialization Scripts
- The `./initdb` directory contains SQL scripts that are automatically executed to set up the database schema and initial data when the MySQL container is started for the first time.

## Networks
- All components are connected via a Docker network named `paymybuddynetwork`, which isolates them from external network traffic.

## Volumes
- `db_paymybuddy`: Persistent volume used by MySQL to store database data. This ensures that data persists across container restarts.

## Stopping and Cleaning Up
To stop the services and remove the containers, networks, and volumes created by Docker Compose, you can use:
```bash
docker-compose down -v
