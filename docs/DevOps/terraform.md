# Terraform

Terraform est un outil d’Infrastructure as Code (IaC) permettant de décrire, provisionner et gérer des ressources cloud ou on-premise de façon déclarative. Il fonctionne avec de nombreux fournisseurs (AWS, Azure, GCP, etc.) et facilite la reproductibilité et la traçabilité de l’infrastructure.

## Installation sur Linux

Téléchargez la dernière version depuis [terraform.io](https://www.terraform.io/downloads.html) ou utilisez :

```sh
sudo apt update
sudo apt install -y wget unzip
wget https://releases.hashicorp.com/terraform/1.8.5/terraform_1.8.5_linux_amd64.zip
unzip terraform_1.8.5_linux_amd64.zip
sudo mv terraform /usr/local/bin/
terraform version
```

## Opérations classiques

### Création d'une infra

1. Créez un fichier `main.tf` :
   ```hcl
   provider "aws" {
     region = "eu-west-1"
   }

   resource "aws_s3_bucket" "bucket" {
     bucket = "mon-bucket-exemple"
   }
   ```
2. Initialisez le projet :
   ```sh
   terraform init
   ```
3. Prévisualisez les changements :
   ```sh
   terraform plan
   ```
4. Appliquez la configuration :
   ```sh
   terraform apply
   ```

### Modification d'une infra

Modifiez le fichier `.tf` (ex : changez le nom du bucket), puis :
```sh
terraform plan
terraform apply
```

### Ajout d'inputs et d'outputs

Utilisez des variables pour rendre votre code réutilisable :

`variables.tf` :
```hcl
variable "bucket_name" {
  description = "Nom du bucket S3"
  type        = string
}
```

`main.tf` :
```hcl
resource "aws_s3_bucket" "bucket" {
  bucket = var.bucket_name
}
```

`outputs.tf` :
```hcl
output "bucket_arn" {
  value = aws_s3_bucket.bucket.arn
}
```

Lancez :
```sh
terraform apply -var="bucket_name=mon-bucket-demo"
```

### Nettoyage d'une infra

Pour supprimer toutes les ressources créées :
```sh
terraform destroy
```

## Stockage du state à distance

Le fichier `terraform.tfstate` contient l’état de l’infrastructure. Pour le travail en équipe, stockez-le à distance (ex : S3) :

```hcl
terraform {
  backend "s3" {
    bucket = "mon-bucket-tfstate"
    key    = "state/terraform.tfstate"
    region = "eu-west-1"
  }
}
```

Initialisez le backend :
```sh
terraform init
```

## Génération d'un graphique

Il est possible de générer un graphique à l'aide de la librairie __Graphviz__

Pour cela il faut installer Graphviz

```sh
sudo apt install graphviz
```

Puis, executer la commande suivante

```sh
terraform graph  | dot -Tsvg -Gfontcolor=blue -Glabel="My AWS infrastructure" > graph.svg
```

## Bonnes pratiques
- Versionnez vos fichiers `.tf` avec git.
- Utilisez des workspaces pour gérer plusieurs environnements (dev, prod).
- Séparez les variables sensibles (credentials) dans des fichiers non versionnés.
- Relisez le plan (`terraform plan`) avant chaque `apply`.
- Utilisez des modules pour factoriser le code.

## Ressources utiles
- [Documentation officielle Terraform](https://www.terraform.io/docs/)
- [Terraform Registry (modules)](https://registry.terraform.io/)
- [Best practices Terraform](https://www.terraform-best-practices.com/)

