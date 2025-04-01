# 🧰 Prérequis et Installation des outils

## 📌 Pourquoi cette section ?

Avant de commencer à **construire des images Docker**, il est essentiel de
s’assurer que votre environnement est prêt. Cette section vous guide dans
l’installation des outils nécessaires pour travailler avec Docker et Python, et
vous aide à vérifier que tout est bien configuré.

## 🎯 Prérequis pour suivre ce TP

- [**Compétences en ligne de
  commande**](https://blog.stephane-robert.info/docs/admin-serveurs/linux/introduction/)
  :
  - Être à l’aise avec un terminal Linux.
  - Connaître les commandes de base (`ls`, `cd`, `mkdir`, `rm`, etc.).
  - Savoir utiliser un éditeur de texte en ligne de commande (`nano`, `vim`,
    etc.).

- [**Notions de base sur les
  conteneurs**](https://blog.stephane-robert.info/docs/conteneurs/introduction/)
  :
  - Comprendre le principe d’un **conteneur**.
  - Savoir à quoi sert une **image Docker** et comment elle est construite.

- [**Notions de base sur les
  fichiers Dockerfile**](https://blog.stephane-robert.info/docs/conteneurs/images-conteneurs/introduction/)
  :
  - Comprendre la structure d’un **Dockerfile**.
  - Savoir utiliser les instructions de base (`FROM`, `RUN`, `COPY`, etc.).

- **Un environnement de développement fonctionnel** :
  - Un système **Linux, macOS, ou Windows avec WSL**.
  - L’installation de **Docker** (et `docker` utilisable depuis le terminal).
  - L’installation de **Python 3** et `pip`.

## 🛠️ Outils à installer

### 🐳 Docker

Docker est l’outil principal pour construire et exécuter des conteneurs.

🔗 [Guide d’installation de
Docker](https://blog.stephane-robert.info/docs/conteneurs/moteurs-conteneurs/docker/)

📌 Une fois installé, vérifiez son bon fonctionnement avec :

```bash
docker --version
docker run hello-world
```

## 🚀 Prochaine étape

Maintenant que votre environnement est prêt, vous allez pouvoir construire votre
première image Docker à partir de **Debian 12** et faire tourner une **API
FastAPI minimale** dans un conteneur !

📌 **Passons maintenant au TP de création d’image Docker !** 🐳🔥

