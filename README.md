# Conteneurisation - Du Docker aux orchestrateurs

Ce dépôt propose un parcours progressif pour comprendre et maîtriser la
conteneurisation, depuis la création d’images Docker jusqu’à l’orchestration
avancée avec Kubernetes.

Avant de plonger dans l'univers des conteneurs Docker, il est essentiel de
comprendre ce qu'est la **containerisation** et comment elle fonctionne. Vous
pouvez consulter mon [**introduction à la
containerisation**](https://blog.stephane-robert.info/docs/conteneurs/).

Ce n'est pas un cours, mais plutôt un guide pour vous aider à vous y retrouver
dans le monde des conteneurs.

Une fois que vous aurez compris les concepts de base, vous pourrez explorer
l'univers des conteneurs et de l'orchestration. Ce dépôt est conçu pour vous
permettre de progresser à votre rythme, en vous fournissant des exercices
pratiques et des exemples concrets.

## 📚 Organisation du dépôt

- [`00-Docker`](./00-Docker-images) — Introduction à Docker, création
  d’images personnalisées, utilisations des volumes et des réseaux.
- [`01-Docker-compose`](./01-Docker-compose) — Composition d’applications
  multi-conteneurs avec Docker Compose.
- [`02-Docker-swarm`](./02-Docker-swarm) — Orchestration native de Docker avec
  Docker Swarm.
- [`03-Kubernetes`](./03-Kubernetes) — Déploiement et gestion avancée avec
  Kubernetes.

## 🎯 Objectifs

- Comprendre les concepts fondamentaux des conteneurs.
- Créer, gérer et orchestrer des conteneurs dans différents environnements.
- Fournir des exemples reproductibles et concrets pour l’apprentissage.

## 🔧 Prérequis

- [Docker](https://blog.stephane-robert.info/docs/conteneurs/moteurs-conteneurs/docker/)
- [Git](https://blog.stephane-robert.info/docs/developper/version/git/introduction/)
- [Minikube](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/minikube/)
- [Vagrant](https://blog.stephane-robert.info/docs/infra-as-code/provisionnement/vagrant/introduction/)
  avec VirtualBox ou
  [KVM](https://blog.stephane-robert.info/docs/virtualiser/type1/kvm/) ou
  [proxmox](https://blog.stephane-robert.info/docs/virtualiser/type1/kvm/)
- [Kubectl](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/kubectl/)
  ou autre outil selon le dossier

Ce dépôt est un support d’apprentissage évolutif, n’hésitez pas à proposer des
améliorations ou à poser des questions via les
[issues](https://github.com/stephrobert/kubernetes-training/issues).

## Mise à jour du dépôt

Je vais continuer à mettre à jour ce dépôt avec de nouveaux exercices et des
améliorations. Pour récupérer les dernières modifications, vous pouvez
simplement exécuter :

```bash
git pull origin main
```

## Contribuer

Bien entendu, vous êtes invités à contribuer à ce dépôt en proposant des
améliorations ou en corrigeant des erreurs. N'hésitez pas à ouvrir une **issue**
ou à soumettre une **pull request**.

## Me soutenir

Si vous appréciez ce travail et souhaitez me soutenir, vous pouvez me payer un
café ☕️:

[![ko-fi](https://www.ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/stephanerobert89902)

## Copyright et licence

Tous les contenus contenus dans ce repo sont :

- Copyright ©2025 Stéphane Robert
- Distribués sous [licence Creative Commons BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)

![Creative Commons BY-SA](https://mirrors.creativecommons.org/presskit/buttons/88x31/png/by-sa.png)
