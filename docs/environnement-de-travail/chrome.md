# Mettre à jour Google Chrome sur linux

Chrome n'ayant pas de solution clé en main pour se mettre à jour de manière autonome sur linux, il faut le faire en ligne de commande.

## Téléchargement du paquet

Il faut en premier lieu récupérer le nouveau paquet :

```sh
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

Cette URL varie rarement. Elle permet de télécharger la dernière version stable de Chrome.

## Installation du paquet

Par la suite il vous suffit juste de l'installer avec le gestionnaire de paquet bas niveau `dpkg` qui va venir surcharger les sources de votre Chrome installé localement avec le nouveau paquet.

```sh
sudo dpkg -i google-chrome-stable_current_amd64.deb
```

Si des dépendances sont manquantes, corrigez-les avec :
```sh
sudo apt-get install -f
```

## Nettoyage

Et enfin, n'oubliez pas de nettoyer !

```sh
rm google-chrome-stable_current_amd64.deb
```

## Vérifier la version installée

Pour vérifier que la mise à jour a bien été prise en compte :

```sh
google-chrome --version
```

## Astuce : mise à jour via le dépôt officiel

Vous pouvez aussi ajouter le dépôt officiel Google Chrome pour bénéficier des mises à jour via `apt` :

```sh
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
echo 'deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main' | sudo tee /etc/apt/sources.list.d/google-chrome.list
sudo apt update
sudo apt install google-chrome-stable
```

## Cas particuliers
- Sur Fedora ou CentOS, utilisez le paquet `.rpm` et la commande `dnf` ou `yum`.
- Pour Chromium (version open source), utilisez simplement :
  ```sh
  sudo apt install chromium-browser
  ```

## Bonnes pratiques
- Fermez toutes les fenêtres Chrome avant la mise à jour pour éviter les conflits.
- Sauvegardez vos sessions importantes.
- Vérifiez régulièrement les mises à jour pour bénéficier des derniers correctifs de sécurité.

## Ressources utiles
- [Documentation officielle Google Chrome](https://support.google.com/chrome/answer/95414?hl=fr)
- [Dépôt Google Chrome](https://www.google.com/chrome/)
