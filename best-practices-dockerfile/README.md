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

## Bonnes Pratiques Appliquées dans le Dockerfile

**![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXddbKH6qg3y2AR29ln6RC2vPUe2bxfbTcIqDhWPmcHCZSA6tH07JHq07DeVJcFImVXF1_NozFA8lLxLQs76g1cTGBRQ9fa0H9-wy2TMBuhAwYlOrjDd0sO0y8PVCwxTazMrKIcidbbceY5fy0zi2NbxYdAw?key=mLqAl_ccMoG4hHcRzSYKpw)**


1.  **Choisir une Image de Base Légère** :
    
    -   Le Dockerfile utilise `amazoncorretto:17-alpine`, une image légère basée sur Alpine Linux, réduisant ainsi la taille globale de l'image Docker.
2.  **Réduire le Nombre de Couches** :
    
    -   Les commandes sont combinées autant que possible pour réduire le nombre de couches dans l'image finale, optimisant ainsi le processus de build.
3.  **Utilisation de `.dockerignore`** :
    
    -   Le fichier `.dockerignore` est utilisé pour exclure les fichiers inutiles du contexte de build, améliorant ainsi les performances de la construction.
4.  **Gestion Correcte des Secrets** :
    
    -   Les secrets et configurations sensibles ne sont pas inclus directement dans le Dockerfile. Ils sont plutôt gérés via des variables d'environnement et des fichiers externes comme `.env`.
5.  **Nettoyer les Artefacts de Build** :
    
    -   Le Dockerfile et `.dockerignore` sont configurés pour éviter d'inclure des artefacts de build temporaires ou inutiles dans l'image finale, la maintenant aussi légère que possible.
6.  **Configurer les Volumes Judicieusement** :
    
    -   Les volumes sont explicitement définis pour garantir que les données persistantes sont correctement gérées, séparant la logique de l'application du stockage des données.
7.  **Utiliser des Variables d'Environnement** :
    
    -   L'application est configurée via des variables d'environnement, définies dans un fichier `.env`, ce qui rend l'application flexible et sécurisée.
8.  **Optimiser les Dépendances** :
    
    -   Seules les dépendances nécessaires à l'environnement de production sont installées, gardant ainsi l'image légère et efficace.
9.  **Éviter les Scripts Multi-Étapes Complexes** :
    
    -   Le Dockerfile évite les scripts trop complexes, ce qui facilite la maintenance et la compréhension.
10.  **Documenter et Commenter** :
    
    -   Le Dockerfile inclut des commentaires pour expliquer les étapes clés et les décisions, le rendant plus accessible aux autres développeurs.

## Vérifications de Build Docker

Docker a introduit une fonctionnalité appelée "Build Checks", qui renforce les standards pendant le processus de création d'image. Elle détecte automatiquement des problèmes comme les images non optimisées ou les vulnérabilités de sécurité, garantissant des images Docker de haute qualité et sécurisées dès le départ.

#### Étapes pour Tester les Build Checks

1.  **Configurer le Dockerfile pour les Build Checks**
    
    Assurez-vous que votre Dockerfile utilise la syntaxe avancée avec les options de vérification activées. Par exemple :
    
 dockerfile:

`# Utilisation de la syntaxe avancée pour les Build Checks `
`# syntax=docker/dockerfile:1`
`# check=error=true FROM amazoncorretto:17-alpine`
`FROM amazoncorretto:17-alpine`
`...`
    
    
  -   **`# syntax=docker/dockerfile:1`** : Indique l'utilisation de la syntaxe avancée pour le Dockerfile.
  -   **`# check=error=true`** : Active les Build Checks et spécifie que toute erreur détectée lors du processus de build doit entraîner un échec du build.
2.  **Exécuter le Build avec les Vérifications Actives**
    
    Pour tester les Build Checks avec Docker, vous pouvez exécuter la commande suivante :
    
    `docker build --check -t votreimage .` 
    
    -   **`--check`** : Cette option active les vérifications de build. Docker analysera le Dockerfile pour détecter les erreurs, les images non optimisées, et les vulnérabilités de sécurité.
    -   **`-t votreimage`** : Cette option tague l'image avec le nom `votreimage`.
    
    Si des problèmes sont détectés, Docker les affichera dans la sortie de la commande, et le build échouera si des erreurs critiques sont trouvées.
    
3.  **Analyser les Résultats du Build**
    
    -   **Sortie sans erreurs** : Si aucune erreur ou vulnérabilité n'est détectée, Docker construira l'image normalement et vous verrez la sortie habituelle indiquant la réussite du build.
    -   **Sortie avec erreurs** : Si des problèmes sont détectés (par exemple, une image non optimisée, des pratiques de sécurité non respectées), Docker affichera des messages d'erreur ou d'avertissement en fonction des paramètres de `check=error=true`. Si l'option `check=error=true` est activée, le build échouera.
    
    Exemples d'erreurs possibles :
    
    -   Utilisation d'une image de base obsolète ou non sécurisée.
    -   Inclusion de fichiers ou de secrets non protégés.
    -   Utilisation de pratiques non optimales dans le Dockerfile.

### Test sans Construction Complète de l'Image

Si vous souhaitez exécuter les Build Checks sans construire entièrement l'image, vous pouvez utiliser l'option `--no-cache` pour éviter l'utilisation des caches de build précédents, tout en forçant la vérification de chaque étape :

bash

Copy code

`docker build --check --no-cache -t votreimage .` 

-   **`--no-cache`** : Empêche l'utilisation des caches, forçant Docker à exécuter chaque étape du Dockerfile, ce qui est utile pour s'assurer que toutes les vérifications sont effectuées même sur des étapes normalement mises en cache.

### Conclusion

En utilisant les Build Checks de Docker, vous pouvez améliorer la qualité et la sécurité de vos images Docker en détectant et en corrigeant les problèmes avant que l'image ne soit construite et déployée. Ces vérifications permettent de s'assurer que votre Dockerfile suit les meilleures pratiques et que votre image est aussi optimisée et sécurisée que possible.

N'hésitez pas à exécuter régulièrement ces checks, surtout lors de l'ajout de nouvelles étapes dans votre Dockerfile ou de la mise à jour des dépendances utilisées dans votre projet.
