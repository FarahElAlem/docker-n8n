# 📋 Installation n8n sur Debian 13 avec Docker

## ✅ Étapes réalisées

### 1️⃣ Vérification de Docker

```bash
docker --version
```

**Résultat :** Docker version 29.1.5 ✅ (déjà installé)

---

### 2️⃣ Création du dossier projet

```bash
mkdir -p ~/n8n
cd ~/n8n
```

---

### 3️⃣ Création du fichier docker-compose.yml

```bash
nano docker-compose.yml
```

**Contenu :**

```yaml
version: '3.8'

services:
  n8n:
    image: n8nio/n8n
    container_name: n8n
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - WEBHOOK_URL=http://192.168.200.158:5678/
    volumes:
      - ./n8n_data:/home/node/.n8n
    networks:
      - n8n-network

networks:
  n8n-network:
    driver: bridge

```

---

### 4️⃣ Lancement initial

```bash
sudo docker compose up -d
```

**Problème rencontré :**
```
Error: EACCES: permission denied, open '/home/node/.n8n/config'
```

---

### 5️⃣ Correction des permissions

```bash
# Arrêt du conteneur
sudo docker compose down

# Correction des permissions
sudo chmod -R 777 ~/n8n/n8n_data

# Relancement
sudo docker compose up -d
```

---

### 6️⃣ Vérification finale

```bash
# Vérifier le statut
sudo docker compose ps

# Consulter les logs
sudo docker logs n8n
```

**Résultat :** ✅ n8n démarre correctement sans erreur

---

## 🌐 Accès à n8n

- **Local :** http://localhost:5678
- **Réseau :** http://192.168.200.158:5678

---

## 🔧 Commandes utiles

### Gestion du conteneur

```bash
# Démarrer
sudo docker compose up -d

# Arrêter
sudo docker compose down

# Redémarrer
sudo docker compose restart

# Voir les logs
sudo docker logs n8n

# Logs en temps réel
sudo docker logs -f n8n

# Statut
sudo docker compose ps
```

### Maintenance

```bash
# Mise à jour de l'image
sudo docker compose pull
sudo docker compose up -d

# Backup des données
sudo tar -czf n8n_backup_$(date +%Y%m%d).tar.gz ~/n8n/n8n_data

# Nettoyage
sudo docker compose down -v  # Supprime aussi les volumes
```

---

## 📂 Structure des fichiers

```
~/n8n/
├── docker-compose.yml       # Configuration Docker
└── n8n_data/               # Données persistantes
    ├── config              # Configuration n8n
    ├── workflows/          # Vos workflows
    └── credentials/        # Credentials chiffrés
```

---

## 🔐 Sécurité (optionnel)

Pour ajouter une authentification basique :

```yaml
environment:
  - N8N_BASIC_AUTH_ACTIVE=true
  - N8N_BASIC_AUTH_USER=admin
  - N8N_BASIC_AUTH_PASSWORD=VotreMotDePasse
```

---

## 🐛 Troubleshooting

### Le conteneur redémarre en boucle

```bash
sudo docker logs n8n
```

### Problème de permissions

```bash
sudo chown -R 1000:1000 ~/n8n/n8n_data
```

### Port déjà utilisé

Modifier le port dans docker-compose.yml :
```yaml
ports:
  - "8080:5678"  # Utiliser 8080 au lieu de 5678
```

---

**Installation réussie ! 🎉**

Date : 21 janvier 2026  
Serveur : 192.168.200.158  
Par : Farah @ Weshore
