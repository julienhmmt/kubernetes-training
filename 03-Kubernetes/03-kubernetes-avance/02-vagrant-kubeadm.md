# 🛠️ TP - Mise en place d'un environnement Kubernetes pour la certification CKA

## 🎯 Objectifs

- **Installer un cluster Kubernetes local** adapté à la préparation de la
  certification CKA.
- **Utiliser Vagrant, Libvirt et Ansible** pour automatiser le déploiement des
  machines virtuelles.
- **Configurer un cluster Kubernetes** avec un ou plusieurs nœud master et un ou
  plusieurs nœud worker.

## 📘 Introduction

La certification **Certified Kubernetes Administrator (CKA)** atteste des
compétences dans la gestion de clusters Kubernetes. Pour s'y préparer
efficacement, il est essentiel de disposer d'un environnement d'apprentissage
flexible et proche des conditions de l'examen. Ce TP répond à ce besoin en
permettant de provisionner rapidement des clusters Kubernetes à l'aide de
**Vagrant**, **Libvirt** et **Ansible**.

## 🏗️ Étape 1 - Prérequis

Avant de commencer, assurez-vous de disposer des éléments suivants :

1. **Une machine hôte** avec au minimum **8 cœurs** et **8 Go de RAM** (plus est
   recommandé).
2. **Systèmes d'exploitation supportés** : Linux ou WSL.
3. **Outils installés** :
   - [Vagrant](https://blog.stephane-robert.info/docs/infra-as-code/provisionnement/vagrant/introduction/)
   - [Libvirt](https://blog.stephane-robert.info/docs/virtualiser/type1/kvm/)
   - [Ansible](https://blog.stephane-robert.info/docs/infra-as-code/gestion-de-configuration/ansible/introduction/)
   - Installer les plugins Vagrant pour Libvirt et Ansible :

     ```bash
     vagrant plugin install vagrant-libvirt
     vagrant plugin install vagrant-hostmanager
     ```

   - Installer les collections Ansible suivantes :

    ```bash
    ansible-galaxy collection install community.general
    ansible-galaxy collection install ansible.posix
    ```

   - Cloner le dépôt CKASandBox :

    ```bash
    git clone https://github.com/stephrobert/CKASandBox.git
    ```

## 🏗️ Étape 2 - Création des machines virtuelles

1. **Configurez le Vagrantfile** :

  Le fichier `Vagrantfile` contient des variables permettant de personnaliser
  le cluster :

  ```ruby
  base_ip_str = "10.240.0.1"
  number_master = 1 # Nombre de nœuds master : 1 ou 3
  cpu_master = 2
  mem_master = 1792
  number_worker = 1 # Nombre de nœuds worker : 1 ou plus
  cpu_worker = 1
  mem_worker = 1024
  config.vm.box = "generic/ubuntu2204" # Image pour toutes les installations
  kubectl_version = "1.31"
  kube_version = "1.31"
  ```

  Adaptez ces paramètres selon les ressources de votre machine et vos besoins.

2. **Lancer le provisionnement des machines** :

  ```bash
  vagrant up
  ```

  Cette commande crée les machines virtuelles définies dans le `Vagrantfile`.

## 🏗️ Étape 3 - Accès au contrôleur et initialisation du cluster

1. **Ajouter une entrée dans le fichier hosts si besoin** :

   Pour faciliter l'accès au contrôleur, ajoutez la ligne suivante à votre
   fichier `/etc/hosts` :

   ```plaintext
   10.240.0.10 controller
   ```

2. **Se connecter au master** :

   ```bash
   ssh vagrant@controller
   ```

   Depuis le nœud master, connectez-vous qur la VM cplane1 :

   ```bash
   ssh cplane1
   ```

3. **Initialiser le cluster Kubernetes** :

   Sur le nœud master, exécutez :

   ```bash
   sudo -i
   kubeadm init --pod-network-cidr=192.168.0.0/16 --control-plane-endpoint "10.240.0.10:6443" --upload-certs --cri-socket unix:///run/containerd/containerd.sock
   ```

   Cette commande initialise le plan de contrôle du cluster. Une fois terminée,
   elle affiche une commande `kubeadm join` à exécuter sur
   les nœuds worker pour les rejoindre au cluster.

   Déconnectez-vous de la VM cplane1 et exécutez la commande `kubeadm join` sur
   les nœuds worker.

4. **Configurer kubectl sur le contrôleur** :

  Revenue sur la VM controller, exécutez les commandes suivantes :

   ```bash
   ssh cplane1 sudo cat /etc/kubernetes/admin.conf > ~/.kube/config
   ```

5. **Vérifier l'état des nœuds** :

   ```bash
   kubectl get nodes -A
   NAME      STATUS     ROLES           AGE   VERSION
   cplane1   NotReady   control-plane   51s   v1.31.6
   ```

   Les nœuds devraient apparaître avec le statut `NotReady`.

## 🏗️ Étape 4 - Installation du réseau (CNI)

1. **Installer Calico** :

   ```bash
   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
   ```

2. **Vérifier l'état des nœuds** :

   Après quelques minutes, les nœuds devraient passer au statut `Ready` :

   ```bash
   kubectl get nodes -A
   cplane1   Ready    control-plane   113s   v1.31.6
   ```

## 🏗️ Étape 7 - Gestion du cycle de vie des machines

1. **Suspendre les machines** :

   Pour libérer des ressources sans détruire les machines :

   ```bash
   vagrant suspend
   ```

2. **Reprendre les machines** :

   Pour reprendre les machines suspendues :

   ```bash
   vagrant resume
   ```

3. **Détruire les machines** :

   Si vous n'avez plus besoin des machines :

   ```bash
   vagrant destroy
   ```

## 🚀 Conclusion

Vous avez maintenant un environnement Kubernetes local, flexible et adapté à la
préparation de la certification CKA. Cet environnement vous permet de pratiquer
diverses opérations, de simuler des pannes et de renforcer vos compétences en
administration Kubernetes. N'hésitez pas à explorer davantage les
fonctionnalités de CKASandBox et à l'adapter à vos besoins spécifiques.

citeturn0search0