# 🏗️ TP - Création de Pods dans Kubernetes

## 🎯 Objectifs

- Comprendre le rôle des **Pods** dans Kubernetes.
- Déployer un premier **Pod** manuellement.
- Explorer les différentes façons de gérer les Pods.

👉 Avant de commencer, assurez-vous d’avoir consulté la documentation sur [les
Pods dans
Kubernetes](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/pods/)

## 🛠️ Étape 1 - Comprendre les Pods

- **Un Pod**, c'est la plus petite unité déployable dans Kubernetes. Il peut
contenir un ou plusieurs **conteneurs** partageant le même réseau et stockage.

- Contrairement aux conteneurs Docker exécutés de manière isolée, Kubernetes
regroupe les conteneurs **dans des Pods** pour mieux gérer la scalabilité et la
disponibilité des applications.

📌 Un Pod peut être **éphémère** : il ne doit pas être utilisé directement en
production sans un contrôleur comme un **Deployment** ou un **ReplicaSet**.

## 🏗️ Étape 2 - Déploiement d’un Pod simple

1️⃣ **Créer un Pod avec `kubectl run`** Testons un premier Pod avec une image
Nginx :

```bash
kubectl run mon-pod --image=nginx
```

2️⃣ **Vérifier son état**

```bash
kubectl get pods
```

Si tout est correct, le Pod doit être **Running**.

3️⃣ **Afficher les détails du Pod**

```bash
kubectl describe pod mon-pod
```

4️⃣ **Supprimer le Pod**

```bash
kubectl delete pod mon-pod
```

📌 **Remarque** : Cette méthode est pratique pour des tests, mais pas adaptée
pour des déploiements en production.

## 📜 Étape 3 - Déploiement avec un fichier YAML

Une meilleure approche est de **définir un Pod avec un manifeste YAML**.

📌 **Créer un fichier `pod-nginx.yaml` :**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mon-pod
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
```

- Appliquer ce fichier :

```bash
kubectl apply -f pod-nginx.yaml
```

- Vérifier son état :

```bash
kubectl get pods
```

- Afficher les logs du conteneur :

```bash
kubectl logs mon-pod
```

- Supprimer le Pod :

```bash
kubectl delete -f pod-nginx.yaml
```

## 🎯 Challenge

**Challenge** : Créer un Pod avec plusieurs conteneurs

- Ajoutez un **deuxième conteneur** (par exemple, Alpine) dans le même Pod.
- Testez si les conteneurs peuvent communiquer entre eux.

