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

- Installer le JDk et maven sous la VM Ubuntu
  `sudo apt install openjdk-17-jre-headless maven`

- Construction de l'application
  `mvn clean install`

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

`docker-compose down -v`

## Best practices applied in the Dockerfile

**![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXddbKH6qg3y2AR29ln6RC2vPUe2bxfbTcIqDhWPmcHCZSA6tH07JHq07DeVJcFImVXF1_NozFA8lLxLQs76g1cTGBRQ9fa0H9-wy2TMBuhAwYlOrjDd0sO0y8PVCwxTazMrKIcidbbceY5fy0zi2NbxYdAw?key=mLqAl_ccMoG4hHcRzSYKpw)**


1.  **Choosing a Lightweight Base Image**:
    
    -   The Dockerfile uses `amazoncorretto:17-alpine`, a lightweight image based on Alpine Linux, thereby reducing the overall size of the Docker image.
2.  **Reducing the Number of Layers**:
    
    -   Commands are combined as much as possible to reduce the number of layers in the final image, optimizing the build process.
3.  **Using `.dockerignore`**:
    
    -   The `.dockerignore` file is used to exclude unnecessary files from the build context, improving the build performance.
4.  **Proper Secret Management**:
    
    -   Secrets and sensitive configurations are not included directly in the Dockerfile. Instead, they are managed through environment variables and external files like `.env`.
5.  **Cleaning Up Build Artifacts**:
    
    -   The Dockerfile and `.dockerignore` are configured to avoid including temporary or unnecessary build artifacts in the final image, keeping it as lightweight as possible.
6.  **Configuring Volumes Wisely**:
    
    -   Volumes are explicitly defined to ensure that persistent data is properly managed, separating application logic from data storage.
7.  **Using Environment Variables**:
    
    -   The application is configured via environment variables, defined in a `.env` file, making the application flexible and secure.
8.  **Optimizing Dependencies**:
    
    -   Only the dependencies necessary for the production environment are installed, keeping the image lightweight and efficient.
9.  **Avoiding Complex Multi-Step Scripts**:
    
    -   The Dockerfile avoids overly complex scripts, making it easier to maintain and understand.
10.  **Documenting and Commenting**:
    
    -   The Dockerfile includes comments to explain key steps and decisions, making it more accessible to other developers.

## Docker Build Checks

Docker has introduced a feature called "Build Checks," which enforces higher standards during the image creation process. It automatically detects issues such as unoptimized images or security vulnerabilities, ensuring high-quality and secure Docker images from the start.

#### Steps to Test Build Checks

1.  **Configuring the Dockerfile for Build Checks**
    
    Ensure that your Dockerfile uses advanced syntax with the verification options enabled. For example:
```
# Utilisation de la syntaxe avancée pour les Build Checks
# syntax=docker/dockerfile:1
# check=error=true FROM amazoncorretto:17-alpine
FROM amazoncorretto:17-alpine
...
```
    
    
- `# syntax=docker/dockerfile:1`: Indicates the use of advanced syntax for the Dockerfile.
- `# check=error=true`: Activates Build Checks and specifies that any errors detected during the build process should cause the build to fail.
  
2. Running the Build with Active Checks

To test Build Checks with Docker, you can run the following command:
    
    `docker build --check -t votreimage .` 
    
-   **`--check`**: This option activates build checks. Docker will analyze the Dockerfile for errors, unoptimized images, and security vulnerabilities.
-   **`-t yourimage`**: This option tags the image with the name `yourimage`.

If issues are detected, Docker will display them in the command output, and the build will fail if critical errors are found.
  
3.  **Analyzing Build Results**
    
-   **Output without errors**: If no errors or vulnerabilities are detected, Docker will build the image normally, and you will see the usual output indicating a successful build.
-   **Output with errors**: If issues are detected (for example, an unoptimized image, insecure practices), Docker will display error or warning messages depending on the `check=error=true` settings. If the `check=error=true` option is enabled, the build will fail.
    
    Examples of possible errors:
    
    -   Using an outdated or insecure base image.
    -   Including unprotected files or secrets.
    -   Using suboptimal practices in the Dockerfile.

### Testing Without Full Image Build

If you want to run Build Checks without fully building the image, you can use the `--no-cache` option to avoid using previous build caches, while forcing the verification of each step:


`docker build --check --no-cache -t votreimage .` 

-   **`--no-cache`**: Prevents using caches, forcing Docker to execute each step of the Dockerfile, which is useful to ensure that all checks are performed even on steps that are usually cached.

### Conclusion

By using Docker's Build Checks, you can improve the quality and security of your Docker images by detecting and correcting issues before the image is built and deployed. These checks ensure that your Dockerfile follows best practices and that your image is as optimized and secure as possible.

Feel free to run these checks regularly, especially when adding new steps to your Dockerfile or updating dependencies used in your project.
