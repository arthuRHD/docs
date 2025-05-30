# Créer et déployer un job kubernetes

Un Job Kubernetes permet d’exécuter une ou plusieurs tâches de façon ponctuelle ou batch, jusqu’à leur complétion. Contrairement à un Deployment ou un Pod classique, le Job garantit que la tâche sera exécutée jusqu’à la réussite (ou l’échec après un certain nombre de tentatives).

## Exemple de manifeste Job YAML

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: exemple-job
spec:
  template:
    spec:
      containers:
      - name: mon-job
        image: mon-image:latest
        command: ["sh", "-c", "echo Hello depuis un job Kubernetes!"]
      restartPolicy: Never
  backoffLimit: 3
```

- `image` : remplacez par l’image de votre application (Python, Go, Java, Node, etc).
- `command` : la commande à exécuter dans le conteneur.
- `restartPolicy: Never` : recommandé pour les jobs batch.
- `backoffLimit` : nombre de tentatives avant de considérer le job en échec.

## Déployer le job

```bash
kubectl apply -f job.yaml
```

Vérifier l’état du job :
```bash
kubectl get jobs
kubectl describe job exemple-job
kubectl logs job/exemple-job
```

## Cas d’usage courants
- Traitement de données ponctuel (ETL, migration de base de données)
- Génération de rapports
- Exécution de scripts de maintenance
- Import/export de fichiers

## Bonnes pratiques
- Utilisez des variables d’environnement pour la configuration (section `env:`).
- Stockez les secrets dans des `Secret` ou `ConfigMap` Kubernetes.
- Prévoyez une gestion des erreurs et des logs dans votre application.
- Nettoyez les jobs terminés avec un `ttlSecondsAfterFinished` :

```yaml
spec:
  ttlSecondsAfterFinished: 3600 # Suppression automatique après 1h
```

## Aller plus loin
- Pour des tâches planifiées, utilisez un `CronJob` Kubernetes.
- Pour des jobs parallèles, ajustez `completions` et `parallelism` :

```yaml
spec:
  completions: 5
  parallelism: 2
```

## Ressources utiles
- [Documentation officielle Kubernetes Job](https://kubernetes.io/fr/docs/concepts/workloads/controllers/job/)
- [Kubernetes CronJob](https://kubernetes.io/fr/docs/concepts/workloads/controllers/cron-jobs/)