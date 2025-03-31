# 🛠️ TP - Gestion des Déployments dans Kubernetes

## 🎯 Objectifs

- **Comprendre le rôle des Déployments**.
- **Créer et manipuler un Déployment** dans Kubernetes.
- **Observer la résilience et la mise à jour des Pods gérés par un Déployment**.

## 📘 Introduction

Dans Kubernetes, un **Déployment** est une ressource qui permet de **gérer les
mises à jour des Pods de manière contrôlée**. Contrairement aux **ReplicaSets**,
les **Déployments** facilitent les mises à jour progressives des applications en
créant de nouveaux Pods tout en supprimant les anciens.

Pour une compréhension approfondie des **Déployments**, consultez le guide
complet sur mon blog :
👉 [Les Déployments
Kubernetes](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/deployments/)

## 🏗️ Étape 1 - Vérifier l’environnement

Avant de commencer, assurez-vous que le **Namespace `dev`** est bien actif.

1. **Lister les Namespaces existants** :

```bash
kubectl get namespaces
```

2. **Définir `dev` comme Namespace par défaut** :

```bash
kubectl config set-context --current --namespace=dev
```

3. Faites le ménage en supprimant les ressources précédentes :

```bash
kubectl get all
kubectl delete <ressource> <name>
```

## 🏗️ Étape 2 - Création d’un Déployment

Un **Déployment** gère non seulement le nombre de réplicas de Pods mais aussi
leur mise à jour et leur remplacement progressif.

1. **Créer un fichier YAML `nginx-deployment.yaml`** :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: dev
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

2. **Appliquer le Déployment** :

```bash
kubectl apply -f nginx-deployment.yaml

deployment.apps/nginx-deployment created
```

3. **Vérifier que les Pods sont bien créés** :

```bash
kubectl get pods -n dev

NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-54b9c68f67-67h9g   1/1     Running   0          13s
nginx-deployment-54b9c68f67-gg5mw   1/1     Running   0          13s
nginx-deployment-54b9c68f67-s292v   1/1     Running   0          13s
```

**Résultat attendu** : Trois Pods **associés au Déployment** sont en cours
d’exécution.

## 🏗️ Étape 3 - Tester la résilience du Déployment

1. **Supprimer un Pod manuellement** :

```bash
kubectl delete pod nginx-deployment-54b9c68f67-gg5mw
pod "nginx-deployment-54b9c68f67-gg5mw" deleted
```

2. **Vérifier la réaction du Déployment** :

```bash
kubectl get pods -n dev
```

```bash
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-54b9c68f67-67h9g   1/1     Running   0          79s
nginx-deployment-54b9c68f67-s292v   1/1     Running   0          79s
nginx-deployment-54b9c68f67-xl7x2   1/1     Running   0          20s
```

**Résultat attendu** : Un **nouveau Pod** a été automatiquement recréé pour
maintenir **trois instances**. Regardez la colonne `AGE` pour voir le nouveau
Pod.

## ⚙️ Étape 4 - Modifier dynamiquement le nombre de réplicas

Le nombre de réplicas peut être modifié **sans réappliquer le fichier YAML**.

1. **Augmenter le nombre de réplicas à 5** :

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

2. **Vérifier que les nouveaux Pods ont été ajoutés** :

```bash
kubectl get pods -n dev

NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-54b9c68f67-67h9g   1/1     Running             0          114s
nginx-deployment-54b9c68f67-dfvlt   1/1     Running             0          3s
nginx-deployment-54b9c68f67-kxr2j   0/1     ContainerCreating   0          3s
nginx-deployment-54b9c68f67-s292v   1/1     Running             0          114s
nginx-deployment-54b9c68f67-xl7x2   1/1     Running             0          55s
```

3. **Diminuer le nombre de réplicas à 2 en éditant le Déployment** :

```bash
code nginx-deployment.yaml
```

Appliquez les changements avec :

```bash
kubectl apply -f nginx-deployment.yaml
```

Vérifiez que les Pods en trop sont supprimés :

```bash
kubectl get pods -n dev
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-54b9c68f67-kxr2j   1/1     Running   0          88s
nginx-deployment-54b9c68f67-s292v   1/1     Running   0          3m19s
```

**Résultat attendu** : Kubernetes **supprime les Pods en trop** pour atteindre
l’état souhaité.

## 📜 Étape 5 - Comprendre la mise à jour progressive d’un Déployment

Contrairement aux ReplicaSets, un **Déployment** permet d’effectuer des mises à
jour progressives en créant de nouveaux Pods avant de supprimer les anciens.

1. **Modifier l’image utilisée par le Déployment** :

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.23
```

2. **Suivre la mise à jour des Pods** :

```bash
kubectl rollout status deployment nginx-deployment

deployment "nginx-deployment" successfully rolled out
```

3. **Vérifier que tous les Pods utilisent la nouvelle image** :

```bash
kubectl get pods -n dev
```

4. **Vérifier l’image utilisée** :

```bash
kubectl describe deployment nginx-deployment | grep Image

    Image:        nginx:1.23
```

**Résultat** : Les anciens Pods ont été remplacés progressivement par des Pods
utilisant la nouvelle image `nginx:1.23`.

## 🧩 Challenge - Expérimentez davantage !

🎯 **Challenge 1 : Tester un rollback**

- Changez l’image en une version invalide et observez l’échec de la mise à jour.
- Revenez à la version précédente avec :

```bash
kubectl rollout undo deployment nginx-deployment
```

🎯 **Challenge 2 : Observer le comportement avec `k9s`**

- Lancez `k9s` et explorez les **Déployments** avec la commande :

```bash
:deploy
```

🎯 **Challenge 3 : Vérifier la robustesse avec `kube-score`**

- Analysez le Déployment pour voir s’il respecte les bonnes pratiques :

```bash
kube-score score nginx-deployment.yaml
```

Aie ! Il y a pas de choses à corriger dans le Déployment. 🚀

C'est à vous de jouer !