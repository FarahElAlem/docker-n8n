# 🐋 Installation Docker Engine sur Debian Trixie

## 📋 Table des matières

- [Introduction](#introduction)
- [Informations système](#informations-système)
- [Prérequis](#prérequis)
- [Étapes d'installation](#étapes-dinstallation)
  - [Étape 1 : Préparation du système](#étape-1--préparation-du-système)
  - [Étape 2 : Configuration du dépôt Docker](#étape-2--configuration-du-dépôt-docker)
  - [Étape 3 : Installation de Docker Engine](#étape-3--installation-de-docker-engine)
  - [Étape 4 : Vérification de l'installation](#étape-4--vérification-de-linstallation)
- [Configuration post-installation](#configuration-post-installation)
- [Commandes utiles](#commandes-utiles)
- [Résolution de problèmes](#résolution-de-problèmes)
- [Références](#références)

---

## 🎯 Introduction

Ce document détaille l'installation complète de **Docker Engine** sur un système **Debian Trixie (Testing)**. L'installation a été réalisée depuis le dépôt officiel Docker pour garantir la version la plus récente et les mises à jour de sécurité.

### Objectifs
- ✅ Installer Docker Engine version officielle
- ✅ Configurer le dépôt Docker pour les futures mises à jour
- ✅ Vérifier le bon fonctionnement du service Docker
- ✅ Préparer l'environnement pour le déploiement de conteneurs

---

## 💻 Informations système

| Paramètre | Valeur |
|-----------|--------|
| **Système d'exploitation** | Debian Trixie (Testing) |
| **Architecture** | AMD64 |
| **Docker Engine** | Version 29.1.3 (build f52814d) |
| **Docker Compose** | Version v5.0.0 |
| **Date d'installation** | 27 Décembre 2025 |
| **Statut** | ✅ Installation réussie |

---

## 📦 Prérequis

Avant de commencer l'installation, assurez-vous que :

- Vous avez un accès root ou sudo sur le système
- Votre système est à jour (`sudo apt update && sudo apt upgrade`)
- Vous disposez d'une connexion internet stable
- Le système dispose d'au moins 2 GB d'espace disque libre

---

## 🚀 Étapes d'installation

### Étape 1 : Préparation du système

#### 1.1 Suppression des versions conflictuelles

Cette première étape consiste à nettoyer le système de toute installation Docker préexistante qui pourrait entrer en conflit avec la version officielle.

**Paquets supprimés :**
- `docker.io` : version Debian de Docker
- `docker-doc` : documentation Docker
- `docker-compose` : ancienne version standalone
- `podman-docker` : alternative à Docker
- `containerd` : runtime de conteneurs
- `runc` : outil d'exécution de conteneurs

**Commande exécutée :**
```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do 
    sudo apt-get remove $pkg
done
```

**Résultat :**
- Tous les paquets ont été correctement supprimés
- Aucun conflit détecté
- Système prêt pour l'installation officielle

---

#### 1.2 Mise à jour et installation des prérequis

Avant d'ajouter le dépôt officiel Docker, nous devons installer les outils de sécurité nécessaires.

**Outils installés :**
- **ca-certificates** : pour valider les certificats SSL/TLS
- **curl** : pour télécharger les clés GPG
- **gnupg** : pour vérifier l'authenticité des paquets Docker

**Commandes exécutées :**
```bash
# Mise à jour de la liste des paquets
sudo apt update

# Installation des outils de sécurité et de téléchargement
sudo apt install -y ca-certificates curl gnupg
```

**Résultat :**
- 24 paquets supprimés (anciennes versions)
- Outils de sécurité installés avec succès
- Système sécurisé pour la suite de l'installation

---

### Étape 2 : Configuration du dépôt Docker

#### 2.1 Préparation du répertoire pour les clés GPG

Création du répertoire sécurisé qui contiendra la clé GPG publique de Docker. Les permissions **0755** permettent à tous les utilisateurs de lire les clés, mais seul root peut les modifier.

**Commandes exécutées :**
```bash
# Création du répertoire pour les clés GPG avec les bonnes permissions
sudo install -m 0755 -d /etc/apt/keyrings

# Téléchargement et installation de la clé GPG officielle Docker
curl -fsSL https://download.docker.com/linux/debian/gpg | \
    sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Application des permissions de lecture pour tous
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

**Résultat :**
- Répertoire `/etc/apt/keyrings` créé avec succès
- Clé GPG Docker téléchargée et installée
- Permissions correctement configurées

---

#### 2.2 Ajout du dépôt Docker à APT

Configuration du dépôt officiel Docker dans les sources APT. La commande détecte automatiquement :
- L'architecture système (**amd64**)
- La version Debian (**trixie**)

**Commande exécutée :**
```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**Vérification :**
```bash
cat /etc/apt/sources.list.d/docker.list
```

**Contenu du fichier :**
```
deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian trixie stable
```

---

#### 2.3 Mise à jour avec le nouveau dépôt Docker

Actualisation de la liste des paquets disponibles en incluant maintenant le dépôt Docker officiel.

**Commande exécutée :**
```bash
sudo apt update
```

**Résultat :**
```
Réception de : 3 https://download.docker.com/linux/debian trixie InRelease [32,5 kB]
Réception de : 4 https://download.docker.com/linux/debian trixie/stable amd64 Packages [23,2 kB]
```

✅ Le dépôt Docker est maintenant actif et fonctionnel !

---

### Étape 3 : Installation de Docker Engine

#### 3.1 Installation complète de Docker

Installation de la suite complète Docker depuis le dépôt officiel.

**Composants installés :**
- **docker-ce** : Docker Community Edition (moteur principal)
- **docker-ce-cli** : Interface en ligne de commande
- **containerd.io** : Runtime de conteneurs
- **docker-buildx-plugin** : Extension pour la construction multi-plateforme
- **docker-compose-plugin** : Outil d'orchestration multi-conteneurs

**Commande exécutée :**
```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io \
    docker-buildx-plugin docker-compose-plugin
```

**Statistiques d'installation :**
- 21 paquets installés
- Taille du téléchargement : 104 MB
- Espace disque utilisé : 421 MB

**Paquets supplémentaires installés :**
- `docker-ce-rootless-extras` : Support pour Docker rootless
- `git-man`, `git` : Système de contrôle de version
- `libcurl3-gnutls` : Bibliothèque de transfert de données
- Diverses bibliothèques de dépendances

---

### Étape 4 : Vérification de l'installation

#### 4.1 Vérification du statut du service Docker

Vérification que le service Docker a bien démarré automatiquement après l'installation.

**Commande exécutée :**
```bash
sudo systemctl status docker
```

**Résultat :**
```
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: enabled)
     Active: active (running) since Sat 2025-12-27 06:42:55 CET; 3min 52s ago
     
Main PID: 16145 (dockerd)
  Tasks: 10
 Memory: 28.1M (peak: 30.1M)
    CPU: 421ms
CGroup: /system.slice/docker.service
        └─16145 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

✅ **Statut du service :**
- ✓ Service actif (running)
- ✓ Activé au démarrage (enabled)
- ✓ Écoute sur le socket Unix `/var/run/docker.sock`
- ✓ PID principal : 16145 (dockerd)

---

#### 4.2 Vérification des versions installées

Vérification des versions de Docker Engine et Docker Compose pour confirmer que tous les composants sont correctement installés.

**Commandes exécutées :**
```bash
docker --version
docker compose version
```

**Résultat :**
```
Docker version 29.1.3, build f52814d
Docker Compose version v5.0.0
```

✅ **Versions installées :**

| Composant | Version |
|-----------|---------|
| Docker Engine | 29.1.3 (build f52814d) |
| Docker Compose Plugin | v5.0.0 |

---

## ⚙️ Configuration post-installation

### Ajouter votre utilisateur au groupe docker

Pour éviter l'utilisation systématique de `sudo` avec les commandes Docker :

```bash
# Ajouter l'utilisateur actuel au groupe docker
sudo usermod -aG docker $USER

# Appliquer les changements (ou redémarrer la session)
newgrp docker
```

### Tester Docker avec un conteneur

```bash
# Tester Docker avec le conteneur hello-world
docker run hello-world
```

### Activer le démarrage automatique de Docker

```bash
# Activer Docker au démarrage (déjà fait lors de l'installation)
sudo systemctl enable docker

# Démarrer Docker maintenant
sudo systemctl start docker
```

---

## 📚 Commandes utiles

### Gestion des conteneurs

```bash
# Lister les conteneurs en cours d'exécution
docker ps

# Lister tous les conteneurs (actifs et arrêtés)
docker ps -a

# Arrêter un conteneur
docker stop <container_id>

# Supprimer un conteneur
docker rm <container_id>

# Voir les logs d'un conteneur
docker logs <container_id>
```

### Gestion des images

```bash
# Lister les images locales
docker images

# Télécharger une image depuis Docker Hub
docker pull <image_name>

# Supprimer une image
docker rmi <image_id>

# Construire une image depuis un Dockerfile
docker build -t <image_name> .
```

### Docker Compose

```bash
# Démarrer les services définis dans docker-compose.yml
docker compose up -d

# Arrêter les services
docker compose down

# Voir les logs des services
docker compose logs

# Lister les services actifs
docker compose ps
```

### Informations système

```bash
# Afficher les informations système Docker
docker info

# Afficher l'utilisation des ressources
docker stats

# Afficher l'espace disque utilisé par Docker
docker system df

# Nettoyer les ressources inutilisées
docker system prune -a
```

---

## 🔧 Résolution de problèmes

### Docker ne démarre pas

```bash
# Vérifier les logs du service Docker
sudo journalctl -u docker.service

# Redémarrer le service Docker
sudo systemctl restart docker
```

### Problèmes de permissions

```bash
# Vérifier les permissions du socket Docker
ls -l /var/run/docker.sock

# Ajuster les permissions si nécessaire
sudo chmod 666 /var/run/docker.sock
```

### Espace disque insuffisant

```bash
# Nettoyer les conteneurs arrêtés
docker container prune

# Nettoyer les images non utilisées
docker image prune -a

# Nettoyer tous les objets inutilisés
docker system prune -a --volumes
```

### Conflits de ports

```bash
# Lister les ports utilisés par Docker
docker ps --format "table {{.Names}}\t{{.Ports}}"

# Arrêter un conteneur utilisant un port spécifique
docker stop $(docker ps -q --filter "publish=<port>")
```

---

## 📖 Références

### Documentation officielle

- [Documentation Docker officielle](https://docs.docker.com/)
- [Installation Docker sur Debian](https://docs.docker.com/engine/install/debian/)
- [Docker Compose documentation](https://docs.docker.com/compose/)
- [Docker Hub](https://hub.docker.com/)

### Ressources complémentaires

- [Meilleures pratiques Docker](https://docs.docker.com/develop/dev-best-practices/)
- [Guide de sécurité Docker](https://docs.docker.com/engine/security/)
- [Dockerfile best practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

### Commandes de référence

- [Docker CLI reference](https://docs.docker.com/engine/reference/commandline/cli/)
- [Compose file reference](https://docs.docker.com/compose/compose-file/)

---

## ✅ Récapitulatif de l'installation

| Étape | Description | Statut |
|-------|-------------|--------|
| 1 | Suppression des versions conflictuelles | ✅ Réussi |
| 2 | Installation des prérequis | ✅ Réussi |
| 3 | Configuration des clés GPG | ✅ Réussi |
| 4 | Ajout du dépôt Docker | ✅ Réussi |
| 5 | Installation Docker Engine | ✅ Réussi |
| 6 | Vérification du service | ✅ Réussi |
| 7 | Vérification des versions | ✅ Réussi |

---

## 🎉 Conclusion

L'installation de Docker Engine sur Debian Trixie a été réalisée **avec succès**. Tous les composants nécessaires sont maintenant opérationnels :

✅ Docker Engine version 29.1.3 installé et fonctionnel  
✅ Docker Compose Plugin v5.0.0 intégré à Docker CLI  
✅ Service Docker actif et configuré pour démarrer automatiquement  
✅ Dépôt officiel Docker configuré pour les mises à jour futures

**Votre système est maintenant prêt à déployer et gérer des conteneurs Docker !**

---

## 📝 Prochaines étapes recommandées

1. **Configurer votre environnement** : Ajouter votre utilisateur au groupe docker
2. **Tester Docker** : Exécuter `docker run hello-world`
3. **Explorer Docker Hub** : Découvrir les images disponibles
4. **Apprendre Docker Compose** : Orchestrer vos conteneurs
5. **Déployer vos applications** : Commencer à conteneuriser vos projets

---

**Système :** Debian Trixie (Testing) - AMD64  
**Docker Engine :** 29.1.3 (build f52814d)  
**Docker Compose :** v5.0.0

---

*Document généré automatiquement à partir des logs d'installation*
