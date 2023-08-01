# Terraform

TODO

## Installation sur Linux

TODO

## Opérations classiques

### Création d'une infra

### Modification d'une infra

### Ajout d'inputs et d'outputs

### Nettoyage d'une infra

TODO

## Stockage du state à distance

TODO

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

