# Utilisation de la syntaxe avancée pour les Build Checks
# syntax=docker/dockerfile:1

FROM amazoncorretto:17-alpine

ARG JAR_FILE=target/paymybuddy.jar

WORKDIR /app

COPY ${JAR_FILE} paymybuddy.jar

CMD ["java", "-jar" , "paymybuddy.jar"]
