# La sécurisation de Kubernetes

Sécuriser un cluster Kubernetes est essentiel pour protéger les applications, les données et l’infrastructure sous-jacente. Voici les principaux axes à considérer pour renforcer la sécurité de vos déploiements Kubernetes.

## Contrôle d'accès basé sur les rôles (RBAC)

RBAC permet de définir précisément qui peut faire quoi dans le cluster.

Exemple de rôle et binding :
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: lecture-pods
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: lecture-pods-binding
  namespace: dev
subjects:
- kind: User
  name: alice
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: lecture-pods
  apiGroup: rbac.authorization.k8s.io
```

- Utilisez le principe du moindre privilège : donnez uniquement les droits nécessaires.
- Privilégiez les `ServiceAccount` pour les applications.

## Sécurisation des secrets

- Stockez les secrets dans des objets `Secret` Kubernetes, jamais en clair dans les manifests.
- Utilisez le chiffrement au repos (`EncryptionConfiguration`).
- Exemple de création d’un secret :

```bash
kubectl create secret generic db-password --from-literal=password=SuperSecret123
```

- Intégrez des solutions comme [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) ou [HashiCorp Vault](https://www.vaultproject.io/) pour une gestion avancée.

## Sécurisation du réseau

- Utilisez des NetworkPolicies pour restreindre la communication entre pods.

Exemple :
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: prod
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

- Isolez les namespaces sensibles.
- Activez le chiffrement des communications (TLS) entre les composants.

## Mise à jour et patching

- Maintenez le cluster et les nodes à jour (Kubernetes, OS, images Docker).
- Surveillez les CVE et appliquez les correctifs rapidement.
- Utilisez des images minimales et scannées (ex : `distroless`, `alpine`).

## Outils de sécurité

- [kube-bench](https://github.com/aquasecurity/kube-bench) : vérifie la conformité CIS de votre cluster.
- [kube-hunter](https://github.com/aquasecurity/kube-hunter) : détecte les failles de sécurité.
- [trivy](https://github.com/aquasecurity/trivy) : scanne les images Docker et les manifests.
- [OPA/Gatekeeper](https://github.com/open-policy-agent/gatekeeper) : applique des politiques de sécurité personnalisées.

## Bonnes pratiques générales
- Désactivez l’accès anonyme à l’API.
- Limitez l’accès au dashboard Kubernetes.
- Activez l’audit logging.
- Surveillez les logs et les alertes de sécurité.

## Ressources utiles
- [Kubernetes Security Best Practices (officiel)](https://kubernetes.io/docs/concepts/security/overview/)
- [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes)
- [OWASP Kubernetes Top 10](https://owasp.org/www-project-kubernetes-top-ten/)