# Générer rapidement un backend en Java

## Générer un projet Spring Boot avec Spring Initializer

Se rendre sur [https://start.spring.io/](https://start.spring.io/).

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

## Déployer un backend en Spring Boot sur AWS

WIP

## Faire un heap dump d'un container light java déployé sur k8s

On utilise l'utilitaire `jcmd` packagé avec tout openjdk.

Quand on a très peu d'outils à disposition on devient créatif. Pour copier des fichiers vers l'extérieur il faut avoir le binaire `tar` d'installé dans le container pour pouvoir executer la commande `kubectl cp <source> <desitination>`.

Quand il n'est pas installer, on peut toujours utiliser la sortie standard et piper ça dans un fichier. On encode en base64 pour éviter de corrompre le fichier avec un mauvais affichage.

```sh
kubectl exec <pod> -- bash -c "jcmd 1 GC.run" # Pour clean avant de record la mémoire
sleep 10
kubectl exec <pod> -- bash -c "jcmd 1 GC.heap_dump filename=/tmp/dump.hprof"
kubectl exec <pod> -- bash -c "base64 /tmp/dump.hprof > /tmp/dump64.hprof"
kubectl exec <pod> -- bash -c "cat /tmp/dump64.hprof" | base64 -d > ~/dump.hprof
```

Il ne vous reste plus qu'à le loader dans VisualVM ou JProfiler.

## Faire un recording JFR d'un container light java déployé sur k8s

Même process pour le transfert de fichier, il n'y a que la commande qui change.

```sh
kubectl exec <pod> -- bash -c "jcmd 1 JFR.start duration=1h delay=5s filename=/tmp/recording.jfr"
# Maintenant on fait nos opérations pendant une heure
kubectl exec <pod> -- bash -c "base64 /tmp/recording.jfr > /tmp/recording64.jfr"
kubectl exec <pod> -- bash -c "cat /tmp/recording64.jfr" | base64 -d > ~/recording.jfr
```

Et ensuite on le load dans VisualVM ou JProfiler.
