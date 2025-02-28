# 🛠️ TP - Gestion des Services dans Kubernetes

## 🎯 Objectifs

- **Comprendre le rôle des Services** dans Kubernetes.
- **Expérimenter les différents types de Services** : ClusterIP, NodePort et
  LoadBalancer.
- **Observer la relation entre un Déployment et un Service** : assurer la
  disponibilité et la découverte des Pods.

## 📘 Introduction

Dans Kubernetes, un **Service** est une ressource qui permet de **découvrir et
d’accéder aux applications** déployées dans un cluster. Il assure la
communication entre les Pods et expose les applications de manière stable et
sécurisée.

Pour une compréhension approfondie des Deployments, consultez le guide complet
sur mon site : [Les Services
Kubernetes](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/services/)

## 🏗️ Étape 1 - Rappel du Déployment

Avant de configurer un Service, vérifions que l’application **Nginx** est
toujours en cours d’exécution avec le **Déployment** créé dans le TP précédent.

1. **Lister les Déployments existants** :

```bash
kubectl get deployments
```

2. **Lister les Pods associés** :

```bash
kubectl get pods -o wide
```

💡 **Si votre Déployment n'existe plus**, appliquez à nouveau le fichier
`nginx-deployment.yaml` utilisé dans le TP précédent :

```bash
kubectl apply -f nginx-deployment.yaml
```

## 🌐 Étape 2 - Création d’un Service ClusterIP

Par défaut, un Pod a une **IP interne éphémère**, ce qui empêche d’autres Pods
d’y accéder de manière stable. Un **Service de type `ClusterIP`** permet
d’attribuer une IP fixe pour rendre l’application accessible aux autres Pods du
cluster.

🔹 **Créer un fichier YAML `nginx-clusterip-service.yaml`** :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

🔹 **Appliquer le Service** :

```bash
kubectl apply -f nginx-clusterip-service.yaml
```

🔹 **Lister les Services disponibles** :

```bash
kubectl get services
```

💡 **Résultat attendu** : Vous devriez voir un Service
**nginx-clusterip-service** avec une **IP interne du cluster**.

---

## 🌍 Étape 3 - Tester l’accessibilité du Service

Pour vérifier que le Service fonctionne, lançons un **Pod temporaire** et
essayons d’accéder à l’application Nginx via l’IP du Service.

🔹 **Créer un Pod temporaire** :

```bash
kubectl run test-pod --image=busybox --restart=Never -- sleep 3600
```

🔹 **Ouvrir un terminal dans ce Pod** :

```bash
kubectl exec -it test-pod -- sh
```

🔹 **Tester la connexion à Nginx via le Service** :

```sh
wget -qO- http://nginx-clusterip-service
```

💡 **Résultat attendu** : La page d’accueil HTML de Nginx s’affiche.

🔹 **Supprimer le Pod temporaire** une fois le test terminé :

```bash
kubectl delete pod test-pod
```

---

## 🔗 Étape 4 - Exposition en dehors du cluster (NodePort)

Un **Service de type `NodePort`** expose l’application sur un port accessible
depuis l’extérieur du cluster.

🔹 **Créer un fichier `nginx-nodeport-service.yaml`** :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30007  # Doit être entre 30000 et 32767
```

🔹 **Appliquer le Service** :

```bash
kubectl apply -f nginx-nodeport-service.yaml
```

🔹 **Vérifier l’IP du nœud** :

```bash
kubectl get nodes -o wide
```

🔹 **Tester l’accès à l’application depuis votre machine** :

```bash
curl http://<IP_DU_NŒUD>:30007
```

💡 **Résultat attendu** : La page HTML de Nginx s’affiche.

---

## 🧩 Challenge - Expérimentez davantage !

🎯 **Challenge 1 : Créer un Service LoadBalancer**
- Sur un **cluster cloud** (GKE, EKS, AKS, Outscale), créez un Service
  `LoadBalancer` et observez son comportement.
- Vérifiez si une **IP publique** lui est attribuée.

🎯 **Challenge 2 : Analyser le Service avec `kube-score`**
[`kube-score`](https://kube-score.com/) est un outil d’analyse statique qui
évalue la robustesse des configurations Kubernetes.

🔹 **Installer kube-score** :

```bash
brew install kube-score  # macOS
```

🔹 **Analyser le Service NodePort** :

```bash
kube-score score nginx-nodeport-service.yaml
```

💡 **Résultat attendu** : `kube-score` donnera des recommandations pour
améliorer la configuration.

🎯 **Challenge 3 : Utiliser `k9s` pour explorer les Services**
- Lancez **k9s** avec :

  ```bash
  k9s
  ```

- Tapez `:svc` pour afficher les Services et observer leur état en temps réel.

---

## 📚 Pour aller plus loin

📖 **Comprendre les Services en profondeur** :
👉 [Guide sur les Services
Kubernetes](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/services/)

---

🚀 **Félicitations !** Vous avez appris à créer et tester des Services dans
Kubernetes pour exposer des applications de manière stable et sécurisée.
Continuez à explorer ces concepts et à expérimenter avec `kube-score` et `k9s`
pour affiner vos déploiements ! 🎯🔥