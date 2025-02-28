# 🛠️ TP - Gestion des ReplicaSets dans Kubernetes

## 🎯 Objectifs

- **Comprendre le rôle des ReplicaSets**
- **Créer et manipuler un ReplicaSet**.
- **Observer la résilience d’un ReplicaSet face aux suppressions de Pods**.

## 📘 Introduction

Dans Kubernetes, un **Replicaset** est une ressource qui permet de **garantir un
nombre précis de réplicas de Pods** en cours d’exécution. Il assure la haute
disponibilité des applications en recréant automatiquement les Pods en cas de
défaillance.

Pour une compréhension approfondie des **Replicasets**, consultez le guide
complet sur mon blog : [Les RéplicaSets
Kubernetes](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/replicasets/)

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

## 🏗️ Étape 2 - Création d’un ReplicaSet

Un **ReplicaSet** garantit qu’un nombre précis de Pods est toujours en cours
d’exécution. Contrairement à un **Déployment**, il ne gère pas les mises à jour
des Pods.

1. **Créer un fichier YAML `nginx-replicaset.yaml`** :

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
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

2. **Appliquer le ReplicaSet** :

```bash
kubectl apply -f nginx-replicaset.yaml

replicaset.apps/nginx-replicaset created
```

3. **Vérifier que les Pods sont bien créés** :

```bash
kubectl get pods -n dev

NAME                     READY   STATUS              RESTARTS   AGE
mon-pod                  1/1     Running             0          16m
nginx-replicaset-h6gn4   1/1     Running             0          16s
nginx-replicaset-vlqr7   0/1     ContainerCreating   0          16s
nginx-replicaset-zbxk4   1/1     Running             0          16s
```

**Résultat attendu** : Trois Pods **associés au ReplicaSet** sont en cours d’exécution.

## Étape 3 - Tester la résilience du ReplicaSet

1. **Lister les Pods pour noter un nom de Pod** :

```bash
kubectl get pods -n dev

NAME                     READY   STATUS    RESTARTS   AGE
mon-pod                  1/1     Running   0          17m
nginx-replicaset-h6gn4   1/1     Running   0          44s
nginx-replicaset-vlqr7   1/1     Running   0          44s
nginx-replicaset-zbxk4   1/1     Running   0          44s
```

2. **Supprimer un Pod manuellement** :

```bash
kubectl delete pod nginx-replicaset-h6gn4

pod "nginx-replicaset-h6gn4" deleted
```

3. **Vérifier la réaction du ReplicaSet** :

```bash
kubectl get pods -n dev
NAME                     READY   STATUS    RESTARTS   AGE
mon-pod                  1/1     Running   0          18m
nginx-replicaset-9zd88   1/1     Running   0          24s
nginx-replicaset-vlqr7   1/1     Running   0          84s
nginx-replicaset-zbxk4   1/1     Running   0          84s
```

**Résultat attendu** : Un **nouveau Pod** a été automatiquement recréé pour
maintenir **trois instances**. Regardez la colonne age pour voir le nouveau Pod.

## ⚙️ Étape 4 - Modifier dynamiquement le nombre de réplicas

Le nombre de réplicas peut être modifié **sans réappliquer le fichier YAML**.

1. **Augmenter le nombre de réplicas à 5** :

```bash
kubectl scale rs nginx-replicaset --replicas=5
```

2. **Vérifier que les nouveaux Pods ont été ajoutés** :

```bash
kubectl get pods -n dev

NAME                     READY   STATUS    RESTARTS   AGE
mon-pod                  1/1     Running   0          18m
nginx-replicaset-9zd88   1/1     Running   0          74s
nginx-replicaset-frzpl   1/1     Running   0          15s
nginx-replicaset-hfxxl   1/1     Running   0          15s
nginx-replicaset-vlqr7   1/1     Running   0          2m14s
nginx-replicaset-zbxk4   1/1     Running   0          2m14s
```

**Diminuer le nombre de réplicas à 2** en éditant le yaml.

```bash
kubectl apply -f nginx-replicaset.yaml

kubectl get pods -n dev

NAME                     READY   STATUS    RESTARTS   AGE
mon-pod                  1/1     Running   0          19m
nginx-replicaset-vlqr7   1/1     Running   0          2m59s
nginx-replicaset-zbxk4   1/1     Running   0          2m59s
```

**Résultat attendu** : Kubernetes **supprime les Pods en trop** pour atteindre
l’état souhaité.


## 📜 Étape 5 - Compendre les limites des replicasets

Un **ReplicaSet** ne permet pas la mise à jour des Pods existants, contrairement
à un **Déployment**, que nous verrons plus tard.

1. **Modifier l’image utilisée par le ReplicaSet** :

```bash
kubectl set image rs nginx-replicaset nginx=nginx:1.23
```

2. **Vérifier si l’image des Pods a changé** :

```bash
kubectl get pods -n dev

NAME                     READY   STATUS    RESTARTS   AGE
mon-pod                  1/1     Running   0          21m
nginx-replicaset-vlqr7   1/1     Running   0          4m43s
nginx-replicaset-zbxk4   1/1     Running   0          4m43s

kubectl describe pod nginx-replicaset-zbxk4 | grep Image

    Image:          nginx:latest
    Image ID:       docker-pullable://nginx@sha256:9d6b58feebd2dbd3c56ab5853333d627cc6e281011cfd6050fa4bcf2072c9496
  Normal  Pulled     4m58s  kubelet            Successfully pulled image "nginx:latest" in 954ms (1.751s including waiting). Image size: 191998640 bytes.
```

**Résultat** : Les **anciens Pods ne sont pas remplacés**. Seuls les nouveaux
Pods utiliseront l’image mise à jour.

3. **Supprimer manuellement tous les Pods et voir l’effet** :

```bash
kubectl delete pods --selector=app=nginx -n dev
pod "nginx-replicaset-vlqr7" deleted
pod "nginx-replicaset-zbxk4" deleted
```

**Résultat** : Les nouveaux Pods utilisent la nouvelle image `nginx:1.23`.

```bash
    Image:          nginx:1.23
    Image ID:       docker-pullable://nginx@sha256:f5747a42e3adcb3168049d63278d7251d91185bb5111d2563d58729a5c9179b0
  Normal  Pulled     5s    kubelet            Successfully pulled image "nginx:1.23" in 18.362s (18.362s including waiting). Image size: 142145851 bytes.
```

## 🧩 Challenge - Expérimentez davantage !

🎯 **Challenge 3 : Vérifier la robustesse avec `kube-score`**

Analysez le ReplicaSet pour voir s’il respecte les bonnes pratiques.

```bash
kube-score score nginx-replicaset.yaml
```

🎯 **Challenge 4 : Utiliser `k9s` pour surveiller les réplicas**

Lancez **k9s** et explorez les **ReplicaSets** avec la commande :

```bash
:rs
```

🚀 **Félicitations !** Vous avez appris à gérer les réplicas de Pods avec un
ReplicaSet et à observer sa résilience. Continuez à expérimenter et à manipuler
les objets Kubernetes pour mieux comprendre leur fonctionnement ! 🎯🔥