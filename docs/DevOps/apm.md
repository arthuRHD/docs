# Mise en place d'une stack de monitoring ELK

## Introduction

La stack ELK (Elasticsearch, Logstash, Kibana) permet de centraliser, visualiser et analyser les logs et métriques d’applications, particulièrement utile dans un environnement Kubernetes où les logs sont éphémères et distribués.

## Elasticsearch

Elasticsearch est le moteur de recherche et d’indexation. Dans Kubernetes, il est recommandé de le déployer via l’[Elastic Operator](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-overview.html) :

```yaml
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: elk-cluster
spec:
  version: 8.13.0
  nodeSets:
  - name: default
    count: 3
    config:
      node.store.allow_mmap: false
```

- Utilisez des volumes persistants pour la durabilité des données.
- Sécurisez l’accès avec des secrets et des NetworkPolicies.

## Kibana

Kibana permet de visualiser les données stockées dans Elasticsearch.

Déploiement via Operator :
```yaml
apiVersion: kibana.k8s.elastic.co/v1
kind: Kibana
metadata:
  name: kibana
spec:
  version: 8.13.0
  count: 1
  elasticsearchRef:
    name: elk-cluster
```

### Alertes & notifications

- Configurez les alertes dans Kibana (Stack Management > Rules and Connectors).
- Exemple : alerte sur le nombre d’erreurs applicatives :
  - Créez une règle de type "Log threshold" sur le champ `log.level: error`.
  - Ajoutez un connecteur (email, Slack, webhook).

### Dashboards

- Utilisez les dashboards prédéfinis de Filebeat/Metricbeat ou créez les vôtres.
- Exemple : dashboard de suivi des erreurs HTTP 5xx, latence des requêtes, etc.

## Logstash

Logstash permet de transformer et router les logs. Dans Kubernetes, il peut être utilisé pour enrichir les logs avant ingestion dans Elasticsearch.

Exemple de pipeline :
```conf
input { beats { port => 5044 } }
filter { grok { match => { "message" => "%{COMBINEDAPACHELOG}" } } }
output { elasticsearch { hosts => ["http://elasticsearch:9200"] } }
```

## Metricbeat

Metricbeat collecte les métriques système et applicatives.

Déploiement en DaemonSet :
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: metricbeat
spec:
  selector:
    matchLabels:
      app: metricbeat
  template:
    metadata:
      labels:
        app: metricbeat
    spec:
      containers:
      - name: metricbeat
        image: docker.elastic.co/beats/metricbeat:8.13.0
        env:
        - name: ELASTICSEARCH_HOSTS
          value: "http://elasticsearch:9200"
        volumeMounts:
        - name: proc
          mountPath: /hostfs/proc
          readOnly: true
        - name: sys
          mountPath: /hostfs/sys
          readOnly: true
      volumes:
      - name: proc
        hostPath:
          path: /proc
      - name: sys
        hostPath:
          path: /sys
```

- Metricbeat peut être configuré pour monitorer les pods, nodes, et services Kubernetes.

## Filebeat

Filebeat collecte et expédie les logs des pods vers Logstash ou Elasticsearch.

Déploiement en DaemonSet :
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: filebeat
spec:
  selector:
    matchLabels:
      app: filebeat
  template:
    metadata:
      labels:
        app: filebeat
    spec:
      containers:
      - name: filebeat
        image: docker.elastic.co/beats/filebeat:8.13.0
        env:
        - name: ELASTICSEARCH_HOSTS
          value: "http://elasticsearch:9200"
        volumeMounts:
        - name: varlog
          mountPath: /var/log
          readOnly: true
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```

- Filebeat peut parser les logs Kubernetes (stdout/stderr des pods).

## APM

Elastic APM permet de tracer les transactions, requêtes et erreurs applicatives.

- Déployez l’APM Server dans le cluster :
  ```yaml
  apiVersion: apm.k8s.elastic.co/v1
  kind: ApmServer
  metadata:
    name: apm-server
  spec:
    version: 8.13.0
    count: 1
    elasticsearchRef:
      name: elk-cluster
    kibanaRef:
      name: kibana
  ```
- Intégrez l’agent APM dans votre application (Java, Python, Node.js, etc) :
  - Ajoutez les variables d’environnement pour pointer vers l’APM Server.
  - Exemple (Java) :
    ```sh
    -Delastic.apm.server_urls=http://apm-server:8200
    -Delastic.apm.service_name=mon-app
    ```
- Visualisez les traces et transactions dans Kibana (menu APM).

## Universal profiling

Universal Profiling (Elastic) permet de profiler le code natif et les performances CPU de toutes les applications du cluster, sans instrumentation.

- Déployez l’agent Universal Profiling via DaemonSet (voir [doc officielle](https://www.elastic.co/guide/en/observability/current/universal-profiling.html)).
- Visualisez les flamegraphs et les hotspots dans Kibana.

## Bonnes pratiques
- Utilisez des namespaces dédiés pour la stack ELK.
- Protégez l’accès à Kibana et Elasticsearch (authentification, RBAC, NetworkPolicy).
- Surveillez la volumétrie des logs pour éviter la saturation du cluster.
- Automatisez le déploiement avec Helm ou l’Elastic Operator.

## Ressources utiles
- [Elastic Cloud on Kubernetes (ECK)](https://www.elastic.co/guide/en/cloud-on-k8s/current/index.html)
- [Elastic APM sur Kubernetes](https://www.elastic.co/guide/en/apm/get-started/current/kubernetes.html)
- [Dashboards Kibana](https://www.elastic.co/guide/en/kibana/current/dashboard.html)