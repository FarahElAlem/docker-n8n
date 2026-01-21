# 🌤️ Exercice 1 : Récupérer la météo en temps réel

## 🎯 Objectif
Créer un workflow n8n qui récupère les données météo de Casablanca en temps réel via une API publique gratuite.

---

## 📋 Ce qu'on a appris

✅ Créer un workflow n8n  
✅ Utiliser un Manual Trigger  
✅ Appeler une API externe avec HTTP Request  
✅ Formater les données avec le node Set  
✅ Utiliser des expressions pour extraire des valeurs  

---

## 🔧 Étapes réalisées

### 1️⃣ Création du workflow

- **New Workflow**
- Nom : `Citation du Jour` (renommé en `Météo Casablanca`)

---

### 2️⃣ Ajout du Manual Trigger

**Node :** Manual Trigger  
**Fonction :** Déclencher le workflow manuellement

---

### 3️⃣ Configuration HTTP Request

**Node :** HTTP Request  
**Configuration :**
- **Method :** GET
- **URL :** `https://api.open-meteo.com/v1/forecast?latitude=33.5731&longitude=-7.5898&current_weather=true`

**Résultat obtenu :**
```json
{
  "latitude": 33.5625,
  "longitude": -7.5625,
  "current_weather": {
    "time": "2026-01-21T20:30",
    "temperature": 13.7,
    "windspeed": 16.7,
    "winddirection": 213,
    "is_day": 0,
    "weathercode": 61
  }
}
```

**Données en temps réel :**
- 🌡️ Température : **13.7°C**
- 💨 Vent : **16.7 km/h**
- 🌙 Moment : **Nuit** (is_day = 0)
- ☁️ Code météo : **61** (pluie légère)

---

### 4️⃣ Formatage des données avec Set

**Node :** Set  
**Fonction :** Extraire et formater les données importantes

**Configuration :**

| Name | Value | Type |
|------|-------|------|
| `ville` | `Casablanca` | String |
| `temperature` | `{{ $json.current_weather.temperature }}°C` | Expression |
| `vent` | `{{ $json.current_weather.windspeed }} km/h` | Expression |
| `moment` | `{{ $json.current_weather.is_day === 1 ? "Jour ☀️" : "Nuit 🌙" }}` | Expression |

**Résultat formaté :**
```json
{
  "ville": "Casablanca",
  "temperature": "13.7°C",
  "vent": "16.7 km/h",
  "moment": "Nuit 🌙"
}
```

---

## 🏗️ Architecture du workflow

```
[Manual Trigger] → [HTTP Request] → [Set]
```

**Détails :**
1. **Manual Trigger** : Lance le workflow manuellement
2. **HTTP Request** : Appelle l'API météo Open-Meteo
3. **Set** : Formate et nettoie les données

---

## 🎓 Concepts appris

### 1. API REST
- Appeler une API publique avec GET
- Récupérer des données JSON en temps réel
- URL avec paramètres (latitude, longitude)

### 2. Expressions n8n
- `{{ $json.chemin.vers.valeur }}` : Extraire une valeur
- `{{ condition ? valeur_si_vrai : valeur_si_faux }}` : Condition ternaire

### 3. Node HTTP Request
- Méthode GET pour récupérer des données
- Pas besoin d'authentification pour les APIs publiques
- Le résultat est automatiquement parsé en JSON

### 4. Node Set
- Créer de nouvelles propriétés
- Transformer les données
- Rendre le résultat plus lisible

---

## 🔍 Exemple d'utilisation réelle

Ce type de workflow peut être utilisé pour :

- 📧 **Envoyer un email quotidien** avec la météo
- 📱 **Notification Slack/Discord** le matin
- 📊 **Stocker les données** dans une base de données
- 🤖 **Chatbot** qui répond à "Quelle est la météo ?"
- ⏰ **Automatisation avec Cron** : météo à 7h chaque matin

---

## 🚀 Améliorations possibles

### Ajout d'un Schedule Trigger
Remplacer Manual Trigger par un Schedule Trigger :
- **Cron :** `0 7 * * *` (tous les jours à 7h)

### Envoi par email
Ajouter un node **Send Email** après Set :
- **Subject :** `Météo Casablanca - {{ $now.format('DD/MM/YYYY') }}`
- **Body :** Template avec les données formatées

### Stocker l'historique
Ajouter un node **Google Sheets** ou **PostgreSQL** :
- Sauvegarder les données chaque jour
- Analyser les tendances

### Multi-villes
Modifier pour récupérer plusieurs villes :
- Casablanca
- Rabat
- Marrakech

---

## 📚 Ressources

### API utilisée
- **Open-Meteo** : https://open-meteo.com/
- Gratuite, sans clé API
- Données météo mondiales

### Documentation n8n
- **HTTP Request node** : https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/
- **Set node** : https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.set/
- **Expressions** : https://docs.n8n.io/code/expressions/

### Autres APIs publiques gratuites
- **JSONPlaceholder** : https://jsonplaceholder.typicode.com/ (données test)
- **CoinGecko** : https://www.coingecko.com/api (crypto-monnaies)
- **REST Countries** : https://restcountries.com/ (infos pays)
- **OpenWeatherMap** : https://openweathermap.org/api (météo avec clé gratuite)

---

## ✅ Résultat final

**Workflow fonctionnel qui :**
- ✅ Récupère la météo en temps réel
- ✅ Formate les données lisiblement
- ✅ Fonctionne sans authentification
- ✅ Peut être exécuté à tout moment

**Temps de réalisation :** ~10 minutes  
**Niveau :** Débutant  
**Date :** 21 janvier 2026  

---

**Par : Farah El Alem**  
**Formation Docker + n8n + Automatisation**
