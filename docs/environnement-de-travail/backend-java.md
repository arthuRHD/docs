# Générer rapidement un backend en Java

## Générer un projet Spring Boot avec Spring Initializer

## Créer une image Docker

Pour créer une image Docker compatible avec une application Spring, il faut surchargé l'image principale de gradle pour pouvoir générer le livrable et ensuite le copié et utilisé l'image du JDK GraalVM pour le runtime.

```dockerfile
FROM gradle:8.1.1-jdk17-alpine AS build
COPY --chown=gradle:gradle . /home/gradle/src
WORKDIR /home/gradle/src
RUN gradle build --no-daemon

FROM ghcr.io/graalvm/jdk-community:17-ol9

EXPOSE 8080

COPY --from=build /home/gradle/src/build/libs/*.jar spring-boot-application.jar

ENTRYPOINT ["java", "-XX:+UnlockExperimentalVMOptions", "-Djava.security.egd=file:/dev/./urandom","-jar","spring-boot-application.jar"]
```
