# Mettre à jour Google Chrome sur linux

Chrome n'ayant pas de solution clé en main pour se mettre à jour de manière autonome sur linux, il faut le faire en ligne de commande.

Il faut en premier lier récupérer le nouveau paquet:

```sh
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

Cette URL varie rarement. Elle permet de télécharger la dernière version stable de Chrome.

Par la suite il vous suffit juste de l'installer avec le gestionnaire de paquet bas niveau `dpkg` qui va venir surcharger les sources
de votre Chrome installé localement avec le nouveau paquet.

```sh
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

Et enfin, n'oubliez pas de nettoyer !

```sh
rm google-chrome-stable_current_amd64.deb
```
