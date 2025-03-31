# 🐳 00 - Construire un conteneur

Ce dossier vous guide pas à pas pour construire une image Docker de plus en plus
complexe et sécurisée, basée sur une API minimale développée en Python avec
FastAPI.

L'objectif est de maîtriser les bonnes pratiques de construction d'image :

- d'abord sur une base **Debian 12**,
- puis migrer vers **Alpine Linux** pour gagner en compacité,
- tout en appliquant petit à petit les bonnes pratiques de **sécurité** et
  d'optimisation :
  - minimisation de la surface d'attaque,
  - optimisation de la taille finale de l'image
  - suppression du compte root,
  - limitation des capabilities.

## 🎯 Objectifs pédagogiques

À travers ces exercices, vous apprendrez à :

- 🏗️ Construire des images Docker à partir de différentes bases.
- 📦 Optimiser la taille des images et réduire la surface d'attaque.
- 🛡️ Analyser et améliorer la sécurité des images Docker.
- 🔄 Utiliser des techniques avancées comme les constructions multi-étapes.

## 🗂️ Structure du projet

- `01-debian12-basique/` : 🐧 Création d'une image Docker basique basée sur Debian 12.
- `02-debian12-optimisee/` : 📉 Optimisation de l'image Debian 12 en réduisant sa taille avec un fichier `.dockerignore` et analyse de la surface d'attaque avec Trivy.
- `03-alpine/` : 🏔️ Migration vers une image Alpine Linux pour une empreinte plus légère et nouvelle analyse avec Trivy.
- `04-wolfi-os/` : 🐺 Utilisation de Wolfi OS comme base pour l'image Docker et évaluation de sa sécurité.
- `05-multistage-build/` : 🏗️ Mise en œuvre d'une construction multi-étapes pour optimiser la taille et la sécurité de l'image finale.

## 🛠️ Prérequis

Avant de commencer, assurez-vous d'avoir installé les outils suivants :

- [Docker](https://blog.stephane-robert.info/docs/conteneurs/moteurs-conteneurs/docker/)
- 🐍 [Python 3](https://blog.stephane-robert.info/docs/developper/programmation/python/)
- 🔍 [Trivy](https://blog.stephane-robert.info/docs/securiser/outils/trivy/) pour l'analyse de vulnérabilités

## 📖 Instructions générales

Chaque dossier contient :

- 📂 Un sous-dossier `app/` avec le code source de l'application FastAPI.
- 📄 Un `Dockerfile` spécifique à l'étape concernée.
- 📝 Un fichier `README.md` détaillant les objectifs, les instructions de construction et d'exécution, ainsi que les points d'apprentissage spécifiques.

Pour chaque étape, consultez le `README.md` correspondant pour des instructions détaillées.

N'hésitez pas à explorer chaque dossier dans l'ordre pour construire progressivement vos compétences en matière de conteneurisation et de sécurisation des applcations Docker.