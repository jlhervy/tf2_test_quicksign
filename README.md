# Utilisation de docker, mlflow et jupyter pour encapsuler un projet de data-science.


## Contexte 

On dispose d'un script python permettant d'entraîner et d'évaluer un modèle de deep-learning sur un jeu de données 'iris'. Le but étant de pouvoir prédire l'espèce d'une fleur en fonction de ses caractéristiques.

Le but est d'utiliser mlflow afin de logger des indicateurs d'entraînement, de scores, etc..., et de les visualiser à l'aide d'une interface utilisateur, tout cela en utilisant docker et jupyter-notebook.




## Travail effectué 

- Conversion du code python train_predict_2.py en Python 3 (pas beaucoup de changements). 
- lint du code train_predict_2.py et conversion en jupyter notebook
- Intégration de Poetry pour gérer les dépendances
- Un venv est créé en local par Poetry, mais docker isole ses propores dépendances (lors du pip install).
- Intégration dans docker avec un docker-compose
- Persistance des logs mlflow et des artifacts (modèles) avec des volumes docker. Cependant, mlflow ne supporte pas le stockage des artifacts directement sur docker, comme pour les logs (https://github.com/mlflow/mlflow/issues/902). Ils sont donc stockés dans deux dossiers différents.

## Description de la structure du projet 

- **./jupyter-notebook-docker/ :** Contient le Dockerfile pour le jupyter-notebook ainsi que le fichier requirements.txt qui liste les dépendances Python.
- **./mlflow-docker/ :** Contient le Dockerfile pour mlflow ainsi qu'un dossier mlruns/ servant de volume docker pour stocker les logs mlflow.
- **./src/ :** Dossier servant de volume docker pour l'image docker jupyter-notebook. Le dossier mlflow-storage/ permet de persister les modèles.
- **pyproject.toml**: Permet de gérer les dépendances avec Poetry.
- **docker-compose.yml**
- **.gitignore/.dockerignore**

## Guide d'utilisation 

Pré-requis : docker et poetry 

### Lancer mlflow et jupyter notebook

Il suffit de se placer dans le projet, puis : 

    docker-compose build && docker-compose up 

Le docker-compose va lancer : 
+ Une image jupyter-notebook accessible à l'adresse *localhost:8888*. Si un token est demandé, il est disponible dans la console où l'on a effectué la commande docker-compose.
+ Un serveur mlflow-tracking permettant de récupérer les logs d'entraînement et de les affichers via l'ui. Cette dernière est accessible à l'adresse *localhost:5000*.


On peut alors lancer des runs sur le notebook, en modifiant au besoin des paramètres, et visualiser les logs avec l'ui mlflow.

### Linter le code jupyter 

Pour linter le code dans jupyter-notebook : 

Tout d'abord, il faut installer ceci :
+ !pip install pycodestyle pycodestyle_magic
+ %load_ext pycodestyle_magic

Puis, on peut linter le code en ajoutant cette commande dans chaque cellule concernée :
+ %%pycodestyle
### Installer une nouvelle dépendance 

Poetry permet de gérer les dépendances et leurs versions efficacement, notamment lors d'un projet de groupe grâce à un 
lockfile permettant de freeze les versions.

Pour ajouter une dépendance, il faut l'installer avec poetry : 

    poetry add <ma-dépendance>
    poetry export -f requirements.txt > jupyter-notebook-docker/requirements.txt
    
Puis, 

    docker-compose build && docker-compose-up
   
Note : poetry ne gère pas pour l'instant tensorflow 2.0.0 : https://github.com/python-poetry/poetry/issues/1330