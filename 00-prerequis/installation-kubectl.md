# Installation de `kubectl`

## Qu'est-ce que `kubectl` ?

`kubectl` est l'outil en ligne de commande permettant d'interagir avec un
cluster Kubernetes. Il est indispensable pour administrer un cluster, gérer les
ressources, déployer des applications et surveiller l'état des services.

## Pourquoi utiliser `kubectl` ?

Avec `kubectl`, il est possible de :

- Créer, mettre à jour et supprimer des ressources Kubernetes (Pods, Services,
  Deployments, etc.).
- Obtenir des informations sur l'état du cluster et des applications déployées.
- Déployer des applications et exécuter des commandes à distance dans des
  conteneurs.
- Gérer la configuration du cluster et interagir avec plusieurs environnements.

## Installation et configuration

Pour installer `kubectl` sur votre système, suivez mon guide détaillé ici :
👉 [Guide
Kubectl](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/kubectl/)

Une fois installé, assurez-vous qu'il est bien configuré et prêt à être utilisé
en testant la connexion avec votre cluster.

## Vérification de l'installation

Après l’installation, il est important de vérifier que `kubectl` est bien
configuré :

```bash
kubectl version
```

Si tout est correctement configuré, vous devriez voir la version de `kubectl` et
du serveur Kubernetes s'afficher.

