# 🛠️ TP - Gestion des Namespaces dans Kubernetes

## 🎯 Objectifs

- **Comprendre le rôle des Namespaces**
- **Créer et manipuler des Namespaces**.
- **Déployer des ressources dans différents Namespaces**.
- **Modifier les manifestes des TP précédents pour intégrer un Namespace.**

## 📘 Introduction

Dans Kubernetes, un **Namespace** est une ressource qui permet de **partitionner
un cluster en plusieurs environnements isolés**. Il offre une **séparation
logique** des ressources pour mieux organiser les déploiements et les services.

Pour une compréhension approfondie des Namespaces, consultez le guide complet
sur mon blog : [Namespaces Kubernetes : isolation et gestion avancée
](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/namespaces/)

## 🏗️ Étape 1 - Comprendre et explorer les Namespaces

1. **Lister les Namespaces existants** :

```bash
kubectl get namespaces
```

Vous verrez au minimum les Namespaces suivants :

```bash
NAME              STATUS   AGE
default           Active   XXd
kube-system       Active   XXd
kube-public       Active   XXd
```

2. **Afficher les ressources d’un Namespace spécifique** :

```bash
kubectl get pods -n kube-system
```

**Ce Namespace contient les composants internes du cluster** comme `coredns`
ou `kube-proxy`.

## 🏗️ Étape 2 - Création et utilisation d’un Namespace

1. **Créer un Namespace nommé `dev`** :

```bash
kubectl create namespace dev
namespace/dev created```

2. **Vérifier sa création** :

```bash
kubectl get namespaces

NAME              STATUS   AGE
default           Active   70d
dev               Active   26s
kube-node-lease   Active   70d
kube-public       Active   70d
kube-system       Active   70d
```

**Résultat attendu** : Le Namespace `dev` doit apparaître dans la liste.

3. **Définir `dev` comme Namespace par défaut** pour éviter de le préciser à
chaque commande. Pour cela, utilisez la [gestion des
contextes](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/kubectl-contexte/)
:

```bash
kubectl config set-context --current --namespace=dev

Context "production" modified.
```

4. **Vérifier que le Namespace actif est bien `dev`** :

```bash
kubectl config view --minify | grep namespace

    namespace: dev
```

## 🏗️ Étape 3 - Déployer des ressources dans un Namespace

Nous allons déployer notre pod **Nginx** dans le Namespace `dev`.

1. **Editez le fichier `pod-nginx.yaml`** :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mon-pod
  namespace: dev
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
```

2. **Appliquer le Déployment** :

```bash
kubectl apply -f pod-nginx.yaml
pod/mon-pod created
```

3.**Vérifier que les Pods sont bien créés dans le Namespace `dev`** :

```bash
kubectl get pods -n dev

NAME      READY   STATUS    RESTARTS   AGE
mon-pod   1/1     Running   0          25s
```

**Résultat attendu** : Un Pod Nginx sont en cours d’exécution dans le
Namespace `dev`.

## 🧩 Challenge - Expérimentez davantage !

🎯 **Challenge 1 : Utilisez les outils kubens/kubectx**

- Utilisez les outils [kubens et kubectx](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/kubens-kubectx/)

🎯 **Challenge 2 : Nettoyer un Namespace entier**

- Supprimez toutes les ressources du Namespace `dev` avec une seule commande.

```bash
kubectl delete namespace dev
```

🎯 **Challenge 3 : Analyser les Namespaces avec `k9s`**

- Lancez
  [**k9s**](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/k9s/)
  et tapez `:ns` pour afficher les Namespaces.
- Naviguez dans chaque Namespace et observez les Pods et Services associés.

🚀 **Bravo !** Vous avez appris à organiser vos ressources Kubernetes avec les
Namespaces et à les utiliser efficacement. Continuez à structurer vos
déploiements pour une meilleure isolation et gestion des environnements ! 🎯🔥