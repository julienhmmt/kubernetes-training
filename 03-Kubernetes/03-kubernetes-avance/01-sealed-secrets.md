# 🛠️ TP - Sécurisation des Secrets Kubernetes avec Kubeseal

## 🎯 Objectifs

- **Comprendre les limites des Secrets Kubernetes** et l’importance de les
  chiffrer.
- **Installer et configurer Kubeseal** pour chiffrer les Secrets.
- **Créer et utiliser un SealedSecret** sécurisé dans un cluster Kubernetes.
- **Gérer la rotation et la mise à jour des SealedSecrets**.

## 📘 Introduction

Dans Kubernetes, les **Secrets** permettent de stocker des informations
sensibles (mots de passe, clés API, certificats). Cependant, ils sont **encodés
en base64**, ce qui ne constitue pas un chiffrement sécurisé.

**Kubeseal** est un outil qui **chiffre les Secrets** afin qu’ils puissent être
stockés en toute sécurité dans un **dépôt Git**. Seul le **cluster Kubernetes**
contenant la clé privée pourra les déchiffrer.

📖 **Guide associé** :
👉 [Chiffrer les Secrets Kubernetes avec
Kubeseal](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/sealed-secrets/)

## 🏗️ Étape 1 - Vérifier l’environnement

Avant de commencer, assurez-vous d’avoir un cluster Kubernetes fonctionnel.

1. **Lister les clusters actifs** :

```bash
kubectl config get-contexts

CURRENT   NAME         CLUSTER          AUTHINFO         NAMESPACE
*         test   multinode-demo   multinode-demo   dev
```

2. **Définir le Namespace `dev` comme actif** :

```bash
kubectl config set-context --current --namespace=dev
```

## 🏗️ Étape 2 - Installation de Kubeseal et du contrôleur SealedSecrets

1. **Sur macOS avec Homebrew** :

```bash
brew install kubeseal
```

2. **Avec `asdf-vm`** :

```bash
asdf plugin add kubeseal
asdf install kubeseal latest
asdf global kubeseal latest
```

3. **Vérifier l’installation** :

```bash
kubeseal --version
```

**Résultat attendu** :

```bash
kubeseal version: 0.28.0
```

## 🏗️ Étape 3 - Déployer le contrôleur SealedSecrets dans le cluster**

Le **SealedSecrets Controller** est un composant qui permet de **déchiffrer**
automatiquement les SealedSecrets en Secrets Kubernetes classiques.

1. **Installer le contrôleur avec Helm** :

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install sealed-secrets bitnami/sealed-secrets --namespace kube-system
```

2. **Vérifier que le contrôleur est bien en cours d’exécution** :

```bash
kubectl get pods -n kube-system | grep sealed-secrets

sealed-secrets-7c69fdd76f-9zzkn          1/1     Running   0             28h
```

## 🏗️ Étape 4 - Création et chiffrement d’un Secret Kubernetes

Nous allons créer un Secret **classique**, puis le **convertir en
SealedSecret**.

1. **Créer un fichier YAML `mon-secret.yaml`** contenant un mot de passe :

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: monsecret
  namespace: dev
type: Opaque
data:
  username: YWRtaW4=   # admin en base64
  password: U3VwZXJTZWNyZXQxMjM=   # SuperSecret123 en base64
```

2. **Appliquer le Secret** :

```bash
kubectl apply -f mon-secret.yaml
```

3. **Vérifier son contenu** (les valeurs sont en base64) :

```bash
kubectl get secret mon-secret -o yaml -n dev
```

**Problème** : Ce fichier **ne doit pas être stocké dans un dépôt Git**, car il
peut être facilement déchiffré.

### **Chiffrement du Secret avec Kubeseal**

1. **Exporter la clé publique du contrôleur SealedSecrets** :

```bash
kubeseal --fetch-cert --controller-name=sealed-secrets --controller-namespace=kube-system > sealed-secrets.pem
```

2. **Générer un SealedSecret à partir du Secret existant** :

```bash
kubeseal --controller-name=sealed-secrets --controller-namespace=kube-system --format=yaml < mon-secret.yaml > monsealedsecret.yaml
```

3. **Examiner le contenu du SealedSecret** :

```yaml
---
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  creationTimestamp: null
  name: monsecret
  namespace: dev
spec:
  encryptedData:
    password: AgBezjldlmRSrtSqw7jTszDuz0OTXZzulMryRI8b4wCVY+NF5um43faEgDPepjuDCzFB9B1v3m9c+ZOwLNnYw2LYZZ3mk+a/J/A7JYc/46BlNzTOyASA37UvE/Hp7YcWWdyMHzTW24op3Lz6+ei0jGbzidmu6LZ3nvf7RMTW/uqMuNIhhhNYWfcYWtDYcccaCE6KLuy0TF04qQad+NjDoEyQYJxNsmidcBc+9s6jWe3/O4QK3QVzQuVzHmeHNBZbRaP9B2OdMmzNaDctQdgsyP9/e01pF4lo0XSWhX3VfD6D0HQ1DsokOBw3ZISO4pXpzsQ/fJEVYDz83SRBAtUBC5K09D4qNvLvGE13L+Cxe43dxp77QOQ+ERmgKNcbmryV0YRVryi4xyS6ZRtjQClFDgdTCb+HRyJpGJp3OdeZFq3f7UfoqRtiln4VIQeN2LHZ5DkhmjMtlZBsSOTt3zts+Zu8OP5/MT69RYFKgWHBcmCJmxK+lUE0+Sv0w0hq5t0AAT+WgxG93z/wWpts0e95ypYAVmHBPOJofoya5TrGTxqnfbaNX1YnumB/53FzxzODUMM5Q623/Oifh1gMslaocnJb2f6BttxbM22751Ekzwx4vbxtxgga7gIdLGR2b4e4lCqNO7txCWw21k6hfgvXuE255StZxajCK2sKFWQSyduhKqEviCd/5wUFwGhBrOCEPZhfcXJP7mqhMlo7K1O+fg==
    username: AgA7JykSHZwrxewjRqifew7w2/7R+V/PBWPBAe30CafeLgdLSm7MA6HfZY72Bmn/hOli9XtBM2v/c59ADPW1rdXJ2o3KCZO0DBMzfm+9HQcHjavhJxXaw0t4tBIMO6drDDgeKzonQ3d3LNsP2/tkyL+xfaDVtQZFH/ehzN5HEO+zfOrW3hpvR+hglkZJqm2Tl7A+GBb1Obebz0X8qNfMr55BYtjHfIypPfJRacz8IABVs+htzqsBUp9z7I25x7N+j45xHPZ4PYveYUhIIEIHUPVhZqZ2FjDrmDFl+rZ7A1FSjPIW5BKdf2VeEpfrbYxX0r2e51KS+gkpxh4HXauCDABRqpmq9JAG7ndnNW7iuuGtD1AHM9KnaTCmxz8Qz8WqPdHQZwKJkdBFIAYwQTdT/kO//AMjZ34ErLIyZQgSPVbN++SQ4g/CcI5rN3Il9lf/zrtYz/s5J5XUZWTMlkb0l3sS6i8sV1kpoCWm/SWGKAOK596CaOEDTuFAyhzjSDr9o8L0AUsHl4VYVErLifJBVqKCL8jdySI1AF3vyPMsq4jnWNmUDUwVGpUD8iurEoGyLbYI4uoDHzJTHUZORH2DS5AN8pdjHnhG4Qg6qHvfSCNRjbVzJePISAjs2T/1KbwspAxWkpTpPYdWxaDxVR8lJdsAYiOJZNqN/Jd7yQaOmuozmDfYyKeDIpb3vOc5bfVzXjpFp3djow==
  template:
    metadata:
      creationTimestamp: null
      name: monsecret
      namespace: dev
    type: Opaque
```

**Résultat attendu** : Les valeurs sont **chiffrées** et ne peuvent être
déchiffrées que par le cluster.

## 🏗️ Étape 5 - Déploiement et utilisation du SealedSecret

1. **Appliquer le SealedSecret** :

```bash
kubectl apply -f monsealedsecret.yaml
```

2. **Vérifier que le SealedSecret a été créé** :

```bash
kubectl get sealedsecrets -n dev
```

3. **Vérifier que Kubernetes a généré un Secret standard** :

```bash
kubectl get secret monsecret -o jsonpath="{.data.password}" | base64 --decode

SuperSecret123
```

## 🏗️ Étape 6 - Mise à jour d’un SealedSecret

Un SealedSecret **ne peut pas être modifié directement**. Il faut générer un
**nouveau fichier chiffré**.

1. **Créer un Secret modifié en YAML** :

```bash
kubectl create secret generic monsecret \
  --from-literal=username=admin \
  --from-literal=password=NouveauMotDePasse2024 \
  -o yaml --dry-run=client > mon-secret.yaml
```

2. **Chiffrer le nouveau Secret** :

```bash
kubeseal --controller-name=sealed-secrets --controller-namespace=kube-system --format=yaml < mon-secret.yaml > monsealedsecret.yaml
```

3. **Appliquer le SealedSecret mis à jour** :

```bash
kubectl apply -f monsealedsecret.yaml
```

4. **Redémarrer les Pods pour appliquer le nouveau secret** :

```bash
kubectl delete pod -l app=monapplication
```

🚀 **Félicitations !** Vous avez appris à sécuriser les Secrets Kubernetes avec
**Kubeseal** et à automatiser leur gestion. Continuez à expérimenter pour
renforcer la sécurité de vos déploiements ! 🔥