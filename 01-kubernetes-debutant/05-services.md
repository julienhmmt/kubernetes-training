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
kubectl get deployments.app

NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           14h
```

2. **Lister les Pods associés** :

```bash
kubectl get pods -o wide
NAME                               READY   STATUS    RESTARTS   AGE   IP            NODE                 NOMINATED NODE   READINESS GATES
nginx-deployment-7f86569fd-f99b8   1/1     Running   0          14h   10.244.0.22   multinode-demo       <none>           <none>
nginx-deployment-7f86569fd-jl5d9   1/1     Running   0          14h   10.244.1.50   multinode-demo-m02   <none>           <none>
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

service/nginx-clusterip-service created
```

🔹 **Lister les Services disponibles** :

```bash
kubectl get services

NAME                      TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
nginx-clusterip-service   ClusterIP   10.96.23.51   <none>        80/TCP    14s
```

**Résultat attendu** : Vous devriez voir un Service
**nginx-clusterip-service** avec une **IP interne du cluster**.

## 🌍 Étape 3 - Tester l’accessibilité du Service

Pour vérifier que le Service fonctionne, lançons un **Pod temporaire** et
essayons d’accéder à l’application Nginx via l’IP du Service.

1. **Créer un Pod temporaire** :

```bash
kubectl run test-pod --image=busybox --restart=Never -- sleep 3600
```

2. **Ouvrir un terminal dans ce Pod** :

```bash
kubectl exec -it test-pod -- sh
```

3. **Tester la connexion à Nginx via le Service** :

```sh
wget -qO- http://nginx-clusterip-service

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

**Résultat attendu** : La page d’accueil HTML de Nginx s’affiche.

4. **Supprimer le Pod temporaire** une fois le test terminé :

```bash
kubectl delete pod test-pod
```

5. Supprimer le Service ClusterIP :

```bash
kubectl delete -f nginx-clusterip-service.yaml
```

## 🔗 Étape 4 - Exposition en dehors du cluster (NodePort)

Un **Service de type `NodePort`** expose l’application sur un port accessible
depuis l’extérieur du cluster.

1. **Créer un fichier `nginx-nodeport-service.yaml`** :

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

2. **Appliquer le Service** :

```bash
kubectl apply -f nginx-nodeport-service.yaml

service/nginx-nodeport-service created
```

3. **Vérifier l’IP du nœud** :

```bash
kubectl get nodes -o wide

NAME                 STATUS   ROLES           AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE              KERNEL-VERSION   CONTAINER-RUNTIME
multinode-demo       Ready    control-plane   70d   v1.31.0   192.168.50.141   <none>        Buildroot 2023.02.9   5.10.207         docker://27.2.0
multinode-demo-m02   Ready    <none>          12d   v1.31.0   192.168.50.38    <none>        Buildroot 2023.02.9   5.10.207         docker://27.2.0
```

4. **Tester l’accès à l’application depuis votre machine** :

```bash
curl http://<IP_DU_NŒUD>:30007

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

**Résultat attendu** : La page HTML de Nginx s’affiche.

## 🧩 Challenge - Expérimentez davantage !

🎯 **Challenge 1 : Analyser le Service avec `kube-score`**
[`kube-score`](https://kube-score.com/) est un outil d’analyse statique qui
évalue la robustesse des configurations Kubernetes.

```bash
kube-score score nginx-nodeport-service.yaml
```

**Résultat attendu** : `kube-score` donnera des recommandations pour
améliorer la configuration.

🎯 **Challenge 3 : Utiliser `k9s` pour explorer les Services**

1. Lancez **k9s** avec :

  ```bash
  k9s
  ```

2. Tapez `:svc` pour afficher les Services et observer leur état en temps réel.

3. Supprimez le Service NodePort

🚀 **Félicitations !** Vous avez appris à créer et tester des Services dans
Kubernetes pour exposer des applications de manière stable et sécurisée.
Continuez à explorer ces concepts et à expérimenter avec `kube-score` et `k9s`
pour affiner vos déploiements ! 🎯🔥