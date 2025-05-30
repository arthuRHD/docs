# Créer et déployer un backend en go

## Initialisation du projet

Pour démarrer un projet Go, commencez par initialiser un module :

```bash
go mod init github.com/mon-org/mon-backend
```

Créez un fichier principal `main.go` :

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, backend Go!")
}
```

Installez les dépendances nécessaires (exemple avec Gin) :

```bash
go get github.com/gin-gonic/gin
```

## Exemple d’API REST avec Gin

```go
package main

import (
    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(200, gin.H{"message": "pong"})
    })
    r.Run() // écoute sur :8080 par défaut
}
```

Lancez le serveur :

```bash
go run main.go
```

## Créer une image Docker

Créez un fichier `Dockerfile` à la racine du projet :

```dockerfile
FROM golang:1.22-alpine AS build
WORKDIR /app
COPY . .
RUN go build -o app

FROM alpine:latest
WORKDIR /root/
COPY --from=build /app/app .
EXPOSE 8080
CMD ["./app"]
```

Construisez et lancez l’image :

```bash
docker build -t mon-backend-go .
docker run -p 8080:8080 mon-backend-go
```

## Déploiement (exemple avec Docker Compose)

Créez un fichier `docker-compose.yml` :

```yaml
version: '3.8'
services:
  backend:
    build: .
    ports:
      - "8080:8080"
```

Lancez le tout :

```bash
docker-compose up --build
```

## Déployer un backend Go sur Kubernetes (k8s)

Pour déployer votre application Go sur Kubernetes, créez un fichier de déploiement `deployment.yaml` :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: go-backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: go-backend
  template:
    metadata:
      labels:
        app: go-backend
    spec:
      containers:
      - name: go-backend
        image: mon-backend-go:latest
        ports:
        - containerPort: 8080
        env:
        - name: ENV
          value: "production"
```

Exposez le service :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: go-backend-service
spec:
  type: LoadBalancer
  selector:
    app: go-backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

Déployez sur le cluster :
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

## Profiling et heap dump d’un container Go sur k8s

Go intègre un outil de profiling très puissant : `pprof`. Ajoutez-le à votre application :

```go
import (
    _ "net/http/pprof"
    "log"
    "net/http"
)

func main() {
    go func() {
        log.Println(http.ListenAndServe("localhost:6060", nil))
    }()
    // ...votre code...
}
```

Exposez le port 6060 dans votre `Dockerfile` et dans le manifest k8s.

Pour récupérer un heap dump :
```bash
kubectl port-forward <pod> 6060:6060
# Puis dans un autre terminal :
curl http://localhost:6060/debug/pprof/heap > heap.out
```
Analysez avec :
```bash
go tool pprof heap.out
```

## Optimisation et bonnes pratiques avancées

- Utilisez `pprof` pour identifier les fuites mémoire ou les points chauds CPU.
- Ajoutez des métriques Prometheus avec [promhttp](https://github.com/prometheus/client_golang).
- Structurez votre projet avec `/cmd`, `/pkg`, `/internal` pour la maintenabilité.
- Utilisez des contextes (`context.Context`) pour gérer les timeouts et annulations.
- Ajoutez des probes liveness/readiness dans vos manifests k8s :

```yaml
livenessProbe:
  httpGet:
    path: /ping
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
readinessProbe:
  httpGet:
    path: /ping
    port: 8080
  initialDelaySeconds: 2
  periodSeconds: 5
```

## Ressources utiles
- [La doc officielle Go](https://golang.org/doc/)
- [Gin Web Framework](https://gin-gonic.com/)
- [Go Docker Best Practices](https://docs.docker.com/language/golang/)
- [Go pprof guide](https://blog.golang.org/pprof)
- [Kubernetes Go example](https://github.com/kelseyhightower/kubernetes-go-example)
- [Prometheus Go client](https://github.com/prometheus/client_golang)