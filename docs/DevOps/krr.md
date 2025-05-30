# Dimensioner un cluster Kubernetes avec KRR

KRR (Kubernetes Resource Recommender) est un outil open source qui analyse l’utilisation réelle des ressources (CPU, mémoire) de vos pods et propose des recommandations pour optimiser les requests/limits. Cela permet de réduire la sur-allocation, d’optimiser les coûts et d’améliorer la stabilité du cluster.

## Installation de KRR

KRR peut être installé en tant que binaire ou via un conteneur Docker.

### Installation via binaire
```sh
curl -L https://github.com/robusta-dev/krr/releases/latest/download/krr-linux-amd64 -o krr
chmod +x krr
sudo mv krr /usr/local/bin/
```

### Installation via Docker
```sh
docker pull ghcr.io/robusta-dev/krr:latest
```

## Connecter au monitoring Prometheus

KRR se connecte à Prometheus pour récupérer les métriques d’utilisation des pods. Il faut fournir l’URL de Prometheus (souvent accessible via un port-forward ou une URL interne du cluster).

Exemple de lancement :
```sh
krr recommend --prometheus-url http://localhost:9090 --namespace my-namespace
```

- Pour accéder à Prometheus depuis l’extérieur du cluster :
  ```sh
  kubectl port-forward svc/prometheus-k8s 9090:9090 -n monitoring
  ```
- Vous pouvez cibler un namespace, un déploiement ou tout le cluster.

KRR génère un rapport avec les recommandations de requests/limits pour chaque pod analysé.

## Visualisation des données dans k9s et Grafana

- **k9s** : Après avoir appliqué les recommandations, utilisez k9s pour visualiser en temps réel l’état des pods, leur consommation et les éventuels OOM ou throttling.
- **Grafana** : Connecté à Prometheus, Grafana permet de visualiser l’évolution de la consommation CPU/mémoire, de comparer les requests/limits actuels et recommandés, et de suivre l’impact des optimisations.

### Exemple de dashboard Grafana
- Utilisez les dashboards communautaires (ex : [Kubernetes / Compute Resources Namespace (Grafana.com)](https://grafana.com/grafana/dashboards/namespace/))
- Ajoutez des panels pour suivre les métriques : `container_cpu_usage_seconds_total`, `container_memory_usage_bytes`, etc.

## Bonnes pratiques
- Appliquez les recommandations KRR d’abord en environnement de test.
- Surveillez l’impact sur la stabilité et la performance après modification.
- Répétez l’analyse régulièrement pour adapter les ressources à l’évolution de la charge.

## Ressources utiles
- [KRR GitHub](https://github.com/robusta-dev/krr)
- [Documentation officielle KRR](https://krr.dev/docs/)
- [Exemple d’intégration Prometheus/Grafana](https://prometheus.io/docs/visualization/grafana/)