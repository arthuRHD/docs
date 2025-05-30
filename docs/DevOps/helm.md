# Regrouper ses manifestes dans un chart Helm

Helm est le gestionnaire de packages de Kubernetes. Il permet de regrouper, paramétrer et déployer facilement des manifestes Kubernetes sous forme de "charts" réutilisables et versionnés.

## Installer Helm

Sous Linux :
```sh
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```
Vérifiez l’installation :
```sh
helm version
```

## Créer un chart Helm

Pour générer la structure d’un nouveau chart :
```sh
helm create mon-chart
```
Cela crée un dossier `mon-chart/` avec la structure suivante :
- `Chart.yaml` : métadonnées du chart
- `values.yaml` : valeurs par défaut
- `templates/` : manifestes Kubernetes templates

## Ajouter des manifestes

Placez vos fichiers YAML Kubernetes dans le dossier `templates/` du chart. Exemple :
- `deployment.yaml`
- `service.yaml`

Vous pouvez utiliser la syntaxe Go templating pour rendre les manifestes dynamiques.

## Ajouter des valeurs

Définissez les variables dans `values.yaml` :
```yaml
replicaCount: 2
image:
  repository: nginx
  tag: latest
```
Dans vos templates :
```yaml
spec:
  replicas: {{ .Values.replicaCount }}
  containers:
    - image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

## Ajouter des templates

Utilisez la syntaxe Helm (`{{ ... }}`) pour paramétrer vos manifestes. Exemple :
```yaml
metadata:
  name: {{ include "mon-chart.fullname" . }}
```
Vous pouvez créer des helpers dans `_helpers.tpl` pour factoriser du code.

## Ajouter des dépendances

Déclarez les dépendances dans `Chart.yaml` :
```yaml
dependencies:
  - name: redis
    version: 18.0.0
    repository: https://charts.bitnami.com/bitnami
```
Installez les dépendances :
```sh
helm dependency update mon-chart
```

## Installer un chart Helm

```sh
helm install mon-app ./mon-chart -n mon-namespace
```
Pour passer des valeurs personnalisées :
```sh
helm install mon-app ./mon-chart -f valeurs-persos.yaml
```

## Mettre à jour un chart Helm

```sh
helm upgrade mon-app ./mon-chart
```

## Supprimer un chart Helm

```sh
helm uninstall mon-app
```

## Ajouter des hooks

Les hooks permettent d’exécuter des jobs avant/après certaines étapes (pre-install, post-upgrade, etc). Exemple dans un template :
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: migration-job
  annotations:
    "helm.sh/hook": pre-install
spec:
  ...
```

## Ajouter des tests

Helm permet d’ajouter des tests automatisés (pods qui doivent réussir). Placez-les dans `templates/tests/` :
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: "test-connection"
  annotations:
    "helm.sh/hook": test
spec:
  containers:
    - name: curl
      image: curlimages/curl
      command: ['curl', '--fail', 'http://mon-service:80']
  restartPolicy: Never
```
Lancez les tests :
```sh
helm test mon-app
```

## Bonnes pratiques
- Versionnez vos charts et documentez les valeurs dans `values.yaml`.
- Utilisez des helpers pour factoriser les noms et labels.
- Testez vos charts en local avec `helm template`.
- Utilisez des schémas de validation (`values.schema.json`).
- Publiez vos charts sur un repository Helm pour les partager.

## Ressources utiles
- [Documentation officielle Helm](https://helm.sh/docs/)
- [Helm Hub (charts)](https://artifacthub.io/)
- [Best practices Helm](https://helm.sh/docs/chart_best_practices/)