# Créer et déployer un package python sur PyPI

Publier un package sur PyPI permet de le rendre accessible à toute la communauté Python via `pip install`. Voici les étapes détaillées pour créer, packager et publier votre module.

## Créer un package en python

Organisez votre projet comme ceci :

```
mon_package/
├── mon_package/
│   ├── __init__.py
│   └── core.py
├── tests/
│   └── test_core.py
├── setup.py
├── README.md
└── LICENSE
```

### Mettre en place le setup.py

Exemple minimal :

```python
from setuptools import setup, find_packages

setup(
    name="mon_package",
    version="0.1.0",
    description="Un exemple de package Python",
    author="Votre Nom",
    packages=find_packages(),
    install_requires=[],
    python_requires=">=3.7",
)
```

Ajoutez un fichier `README.md` pour la description longue, et un fichier `LICENSE` (MIT, Apache2, etc).

### Bonnes pratiques

- Ajoutez des tests unitaires dans le dossier `tests/`.
- Utilisez un `.gitignore` adapté (voir [github/gitignore/python](https://github.com/github/gitignore/blob/main/Python.gitignore)).
- Documentez vos fonctions avec des docstrings.
- Versionnez votre code avec git.
- Ajoutez un fichier `pyproject.toml` pour la compatibilité avec les outils modernes :

```toml
[build-system]
requires = ["setuptools", "wheel"]
build-backend = "setuptools.build_meta"
```

## Déploiement sur la plateforme

1. Installez les outils nécessaires :
   ```bash
   pip install build twine
   ```
2. Générez les artefacts :
   ```bash
   python -m build
   # ou
   python setup.py sdist bdist_wheel
   ```
3. Publiez sur TestPyPI pour tester :
   ```bash
   twine upload --repository testpypi dist/*
   # puis vérifiez sur https://test.pypi.org/project/mon_package/
   ```
4. Publiez sur PyPI :
   ```bash
   twine upload dist/*
   # puis vérifiez sur https://pypi.org/project/mon_package/
   ```

## Intégration avec libraries.io

[libraries.io](https://libraries.io/) référence automatiquement les packages publiés sur PyPI. Pour améliorer la visibilité :
- Ajoutez un lien vers le repo GitHub dans le champ `url` de `setup.py`.
- Ajoutez des badges (PyPI version, build, etc) dans votre `README.md`.

## Ressources utiles
- [Packaging Python Projects (officiel)](https://packaging.python.org/tutorials/packaging-projects/)
- [TestPyPI](https://test.pypi.org/)
- [Twine](https://twine.readthedocs.io/en/stable/)
- [libraries.io](https://libraries.io/)