# Gitlab

GitLab est une plateforme DevOps complète qui propose la gestion du code source, l’intégration continue (CI), le déploiement continu (CD), l’hébergement de pages statiques et de registres Docker privés.

## Intégration continue avec Gitlab CI

GitLab CI permet d’automatiser les tests, la construction et le déploiement de vos applications via un fichier `.gitlab-ci.yml` à la racine du dépôt.

Exemple minimal :

```yaml
stages:
  - test

test_job:
  stage: test
  script:
    - echo "Lancement des tests"
    - pytest
```

- Chaque `job` s’exécute dans un runner GitLab.
- Les étapes (`stages`) définissent l’ordre d’exécution (ex : build, test, deploy).
- Les variables d’environnement peuvent être définies dans l’interface GitLab ou dans le fichier YAML.

## Déploiement continu de documentation avec Gitlab Pages

GitLab Pages permet d’héberger des sites statiques (documentation, blogs, etc.) directement depuis un dépôt GitLab.

Exemple de pipeline pour MkDocs :

```yaml
pages:
  image: python:3.11
  stage: deploy
  script:
    - pip install mkdocs
    - mkdocs build
    - mv site public
  artifacts:
    paths:
      - public
  only:
    - main
```

- Le dossier `public` est publié automatiquement.
- L’URL du site est du type : `https://<namespace>.gitlab.io/<project>`

## Hébergement de livrables docker avec Gitlab Registry

GitLab propose un registre Docker privé intégré à chaque projet.

Exemple de pipeline pour builder et pousser une image :

```yaml
build-image:
  image: docker:latest
  services:
    - docker:dind
  stage: build
  script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE:latest .
    - docker push $CI_REGISTRY_IMAGE:latest
```

- `$CI_REGISTRY_IMAGE` est une variable prédéfinie pointant vers le registre du projet.
- Les identifiants sont injectés automatiquement par GitLab CI.

## Bonnes pratiques

- Versionnez vos pipelines avec `.gitlab-ci.yml`.
- Utilisez des variables de projet pour les secrets.
- Séparez les étapes (build, test, deploy) pour plus de clarté.
- Nettoyez régulièrement les images Docker inutilisées dans le Registry.

## Ressources utiles

- [Documentation GitLab CI/CD](https://docs.gitlab.com/ee/ci/)
- [GitLab Pages](https://docs.gitlab.com/ee/user/project/pages/)
- [GitLab Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/)
