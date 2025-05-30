# AWS

AWS est une plateforme regroupant de nombreux services d'hébergement (CPU, Base de données, Fichiers statiques). C'est une IaaS (Infrastructure as a Service).

[![Watch the video](https://img.youtube.com/vi/JIbIYCM48to/hqdefault.jpg)](https://youtu.be/JIbIYCM48to)

## Avantages

- Agilité, augmente l'agilité de l'entreprise
- Modèle de paiement à l'utilisation, echange de l'argent contre du CPU
- Facilite l'agrandissement d'une infrastructure IT d'une entreprise
- Permet d'être atteignable depuis le monde entier en quelques minutes
- Sécurité, à travers le modèle de responsabilité partagé
- Résilience et disponible, performe comme promis par AWS  
- Elasticité, permet d'ajouter ou de supprimer rapidement des ressources automatiquement

![Shared responsability model](shared_responsibility.png) 

## Mode d'accès

Chaque mode d'accès dépend de l'utilisation. 

Le site est excellent pour prendre en main les services AWS et configurer les budgets comme il le faut.

L'interface en ligne de commande, permet de gagner du temps en faisant abstraction de la terrible navigation du site. C'est utile si nous voulons aller droit au but. 

C'est en général utilisé pour automatiser des actions/déploiement répétitifs. Cependant, cela demande de la configuration en amont et il faut faire attention où nous persistons notre configuration AWS sur notre machine locale. Le SDK présente les mêmes avantages & inconvénients que le CLI. Il en va de même pour l'utilisation avec Terraform.

### CLI

Pour intéragir avec AWS depuis le terminal. Il faut récupérer un clé d'accès depuis notre session connectée sur le site.
Il faut cliquer sur notre compte en haut à droite et aller dans __Informations d'identification de sécurité__ ou dans le service __IAM__.

Ensuite, il vous reste plus qu'à installer le CLI

```sh
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Si vous l'avez déjà sur votre machine locale mais que le client n'est pas à jour, vous pouvez le mettre à jour comme ceci

```sh
$ sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
```

Puis, vérifiez que le client est bien installé et trouvable dans le terminal

```sh
$ aws --version
```

Enfin, on configure avec la clé d'accès générée.

```sh
$ aws configure
```

Si vous rencontrez des soucis de configuration et qui vous avez un besoin urgent d'utiliser le CLI, vous pouvez regarder le service [CloudShell](https://eu-west-3.console.aws.amazon.com/cloudshell/home?region=eu-west-3#) qui pré-configuré et possède même des langages de programmation installé.

Si vous avez besoin d'informations plus poussés concernant le CLI d'AWS, je vous encourage à aller visiter la documentation décrivant chaque commande et sous-commande [ici](https://docs.aws.amazon.com/cli/latest/index.html).

Il existe une autocomplétion possible dans votre terminal, la configuration est expliquée [ici](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-completion.html#cli-command-completion-linux).

### Terraform

Terraform permet de décrire et provisionner des ressources AWS de façon déclarative. Exemple de création d'un bucket S3 :

```hcl
provider "aws" {
  region = "eu-west-1"
}

resource "aws_s3_bucket" "bucket" {
  bucket = "mon-bucket-exemple"
}
```

Initialisez et appliquez :
```sh
terraform init
terraform apply
```

## IAM

IAM (Identity and Access Management) permet de gérer les utilisateurs, groupes, rôles et permissions.

- Créez des utilisateurs avec des permissions minimales.
- Utilisez des groupes pour gérer les droits par équipe.
- Privilégiez les rôles IAM pour les applications et services (ex : EC2, Lambda).

Exemple de création d’un utilisateur via CLI :
```sh
aws iam create-user --user-name dev-user
aws iam attach-user-policy --user-name dev-user --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
```

## S3

S3 est un serveur de fichiers qui permet de stocker à distance des fichiers plats. On peut copier, déplacer et supprimer de manière bidirectionnel.
Il est très souvent utilisé pour stocker des fichiers média en tout genre ou des configurations.

```sh title="Intéractions avec le CLI"
$ aws s3api create-bucket --bucket arichard --region us-east-1 # (1)
{
    "Location": "/arichard"
}

$ aws s3 cp mkdocs.yml s3://arichard/mkdocs.yml # (2)
upload: ./mkdocs.yml to s3://arichard/mkdocs.yml  

$ aws s3 cp s3://arichard/mkdocs.yml mkdocs2.yml # (3)
download: s3://arichard/mkdocs.yml to ./mkdocs2.yml

$ rm mkdocs2.yml && aws s3 rm s3://arichard/mkdocs.yml # (4)
delete: s3://arichard/mkdocs.yml

$ aws s3api delete-bucket --bucket arichard # (5)
```

1. On créer un bucket S3 en lui fournissant le nom. Il n'y que la région us-east-1 qui supporte les buckets s3 pour le moment.
2. On copie un fichier local vers le S3 en lui fournissant le chemin cible.
3. On télécharge un fichier sur le S3 avec son chemin et l'endroit où nous voulons le persister sur la machine locale.
4. On supprime le fichier local téléchargé et le fichier distant.
5. On supprime le S3 en lui fournissant le nom.

## EC2

EC2 est un service de création de machine virtuelle en cloud (Elastic Compute Cloud).

### Créer une instance
```sh
aws ec2 run-instances --image-id ami-0abcdef1234567890 --count 1 --instance-type t2.micro --key-name my-key --security-group-ids sg-0123456789abcdef0 --subnet-id subnet-6e7f829e
```

### Afficher les méta-données des instances
```sh
aws ec2 describe-instances
```

### Modifier une instance déjà créée
Exemple : changer le type d’instance :
```sh
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --instance-type "t2.small"
aws ec2 start-instances --instance-ids i-1234567890abcdef0
```

### Faire communiquer deux instances entre-elles
- Placez-les dans le même VPC et le même groupe de sécurité.
- Ouvrez les ports nécessaires dans le Security Group.

### Nettoyer les ressources
```sh
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
```

## Scaling

AWS propose deux manières du supporter la charge entrante. Il y a :

- Auto scaling groups,
- ELB (Elastic Load Balancer)

### Auto scaling groups

Un groupe échelonne horizontalement les ressources en fonction d'un critère donné (exemple: créer une nouvelle instance si le CPU dépasse 80% d'utilisation).
Un groupe permet de définir un nombre minimum, désiré et maximum d'instances répliquées.

### EJB

Un EJB absorbe la charge la répartissant afin d'obtenir la plus faible latence. Il existe deux types de load balancer: applicatif et réseau.
l'EJB applicatif sert à répartir le traffic sur plusieurs EC2 tandis que l'EJB Réseau absorbe en amont le traffic réseau.

TODO

## EBS

EBS (Elastic Block Store) fournit des volumes de stockage persistant pour EC2.

- Créez un volume :
  ```sh
  aws ec2 create-volume --availability-zone eu-west-1a --size 10 --volume-type gp2
  ```
- Attachez-le à une instance :
  ```sh
  aws ec2 attach-volume --volume-id vol-1234567890abcdef0 --instance-id i-1234567890abcdef0 --device /dev/xvdf
  ```

## RDS

RDS (Relational Database Service) permet de déployer des bases de données managées (MySQL, PostgreSQL, etc).

- Créez une instance :
  ```sh
  aws rds create-db-instance --db-instance-identifier mydb --db-instance-class db.t3.micro --engine postgres --master-username admin --master-user-password password --allocated-storage 20
  ```
- Supprimez une instance :
  ```sh
  aws rds delete-db-instance --db-instance-identifier mydb --skip-final-snapshot
  ```

## DynamoDB

DynamoDB est une base NoSQL managée, scalable et performante.

- Créez une table :
  ```sh
  aws dynamodb create-table --table-name MaTable --attribute-definitions AttributeName=Id,AttributeType=S --key-schema AttributeName=Id,KeyType=HASH --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1
  ```

## Lambda

Lambda permet d’exécuter du code sans gérer de serveur (serverless).

### Déployer une Lambda sous forme de conteneur Docker
- Construisez l’image Docker avec votre code et un Dockerfile compatible Lambda.
- Publiez l’image sur ECR.
- Créez la fonction Lambda en spécifiant l’URL de l’image.

### Paramétrer les autorisations de la fonction
- Attribuez un rôle IAM à la Lambda pour accéder à d’autres services (S3, DynamoDB, etc).

### Nettoyer les ressources
```sh
aws lambda delete-function --function-name ma-fonction
```

## SQS & SNS

- SQS (Simple Queue Service) : file d’attente pour la communication asynchrone.
- SNS (Simple Notification Service) : notifications push/pub-sub.

Exemple : créer une file SQS
```sh
aws sqs create-queue --queue-name MaFile
```

## CloudWatch

CloudWatch permet la collecte de logs, la création d’alarmes et le monitoring des ressources.

### Créer un groupe de log
```sh
aws logs create-log-group --log-group-name MonGroupe
```

### Créer ses propres KPI
- Utilisez les métriques personnalisées :
  ```sh
  aws cloudwatch put-metric-data --metric-name MaMetrice --namespace MonApp --value 42
  ```

### Créer des alarmes
```sh
aws cloudwatch put-metric-alarm --alarm-name CPUHigh --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 300 --threshold 80 --comparison-operator GreaterThanThreshold --dimension Name=InstanceId,Value=i-1234567890abcdef0 --evaluation-periods 2 --alarm-actions arn:aws:sns:... --unit Percent
```

### Mettre en place un workflow de monitoring
- Utilisez CloudWatch Events pour déclencher des actions (ex : Lambda) sur alarme.

### Nettoyer les ressources
```sh
aws logs delete-log-group --log-group-name MonGroupe
```

## API Gateway

API Gateway permet d’exposer des APIs REST ou WebSocket managées, avec intégration Lambda, authentification, throttling, etc.

- Créez une API :
  ```sh
  aws apigateway create-rest-api --name "MonAPI"
  ```

## ECR

ECR (Elastic Container Registry) est un registre Docker managé.

- Créez un repo :
  ```sh
  aws ecr create-repository --repository-name mon-repo
  ```
- Pushez une image :
  ```sh
  aws ecr get-login-password | docker login --username AWS --password-stdin <account-id>.dkr.ecr.<region>.amazonaws.com
  docker tag mon-image:latest <account-id>.dkr.ecr.<region>.amazonaws.com/mon-repo:latest
  docker push <account-id>.dkr.ecr.<region>.amazonaws.com/mon-repo:latest
  ```

## Kubernetes

AWS propose EKS (Elastic Kubernetes Service) pour déployer des clusters Kubernetes managés.

- Créez un cluster avec l’outil [eksctl](https://eksctl.io/) :
  ```sh
  eksctl create cluster --name mon-cluster --region eu-west-1 --nodes 2
  ```
- Déployez vos applications comme sur n’importe quel cluster Kubernetes.

## Bonnes pratiques
- Utilisez IAM pour contrôler finement les accès.
- Activez la MFA sur les comptes sensibles.
- Surveillez vos coûts avec AWS Budgets.
- Automatisez vos déploiements avec CI/CD (CodePipeline, GitHub Actions, GitLab CI).
- Nettoyez régulièrement les ressources inutilisées.

## Ressources utiles
- [Documentation AWS](https://docs.aws.amazon.com/)
- [AWS CLI Reference](https://docs.aws.amazon.com/cli/latest/index.html)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [AWS Free Tier](https://aws.amazon.com/free/)
