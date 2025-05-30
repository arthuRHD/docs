# Faire des tests de charge avec k6

[k6](https://k6.io/) est un outil open source moderne pour réaliser des tests de charge et de performance sur des APIs ou des sites web. Il permet de simuler de nombreux utilisateurs virtuels, de mesurer la latence, le taux d’erreur et d’identifier les points de contention.

## Installation

Sous Linux :
```sh
sudo apt update
sudo apt install -y gnupg2 ca-certificates
curl -s https://dl.k6.io/key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/k6-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
sudo apt update
sudo apt install k6
```

## Exemple de script de test

Créez un fichier `script.js` :
```js
import http from 'k6/http';
import { check, sleep } from 'k6';

export let options = {
  vus: 10, // nombre d'utilisateurs virtuels
  duration: '30s', // durée du test
};

export default function () {
  let res = http.get('https://test-api.example.com/health');
  check(res, {
    'status is 200': (r) => r.status === 200,
  });
  sleep(1);
}
```

## Lancer un test de charge

```sh
k6 run script.js
```

Vous obtiendrez un rapport en temps réel avec les métriques principales : requêtes/s, latence, taux d’erreur, etc.

## Scénarios avancés

- **Augmenter progressivement la charge** :
  ```js
  export let options = {
    stages: [
      { duration: '1m', target: 20 }, // montée en charge
      { duration: '3m', target: 20 }, // palier
      { duration: '1m', target: 0 },  // descente
    ],
  };
  ```
- **Tests sur endpoints protégés** : ajoutez des headers d’authentification (Bearer, Basic, etc).
- **Tests de POST** :
  ```js
  http.post('https://test-api.example.com/login', JSON.stringify({user: 'foo', pass: 'bar'}), { headers: { 'Content-Type': 'application/json' } });
  ```

## Analyse des résultats

- Analysez les métriques : latence moyenne, 95e percentile, taux d’erreur.
- Exportez les résultats en JSON :
  ```sh
  k6 run --out json=résultats.json script.js
  ```
- Intégrez k6 dans vos pipelines CI/CD pour tester la robustesse à chaque déploiement.

## Bonnes pratiques
- Commencez par des tests simples, puis complexifiez les scénarios.
- Testez en environnement de préproduction pour éviter d’impacter la prod.
- Surveillez les ressources du serveur cible pendant les tests (CPU, RAM, logs).
- Documentez vos scripts et résultats pour faciliter le suivi.

## Ressources utiles
- [Documentation officielle k6](https://k6.io/docs/)
- [Exemples de scripts](https://k6.io/docs/examples/)
- [Intégration CI/CD](https://k6.io/docs/integrations/)

## Automatiser le lancement des tests de charge

Pour automatiser l’exécution des tests de charge k6, vous pouvez intégrer le lancement dans vos scripts shell, Makefile ou pipelines CI/CD (GitLab CI, GitHub Actions, etc).

### Exemple avec un Makefile

```makefile
test-charge:
	k6 run script.js
```

Lancez simplement :
```sh
make test-charge
```

### Exemple avec GitLab CI

Ajoutez un job dans `.gitlab-ci.yml` :
```yaml
test_performance:
  image: grafana/k6:latest
  script:
    - k6 run script.js
  only:
    - main
```

### Exemple avec GitHub Actions

```yaml
name: Test de charge k6
on: [push]
jobs:
  k6:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Installer k6
        run: |
          sudo apt-get update && sudo apt-get install -y gnupg2 ca-certificates
          curl -s https://dl.k6.io/key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/k6-archive-keyring.gpg
          echo "deb [signed-by=/usr/share/keyrings/k6-archive-keyring.gpg] https://dl.k6.io/deb stable main" | sudo tee /etc/apt/sources.list.d/k6.list
          sudo apt-get update && sudo apt-get install -y k6
      - name: Lancer le test de charge
        run: k6 run script.js
```

Automatiser les tests permet de garantir la performance à chaque modification ou déploiement.