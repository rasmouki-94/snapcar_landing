# Configuration de l'API Waitlist - Snapcar

## 📋 Vue d'ensemble

Le formulaire de waitlist est configuré pour envoyer les données à une API backend. Cette documentation explique comment configurer l'endpoint API.

## 🔧 Configuration

### 1. Modifier l'URL de l'API

Dans le fichier `snapcar-landing.html`, localisez la section de configuration (ligne ~1578) :

```javascript
const API_CONFIG = {
    WAITLIST_ENDPOINT: 'https://votre-api.com/api/waitlist',
};
```

Remplacez `'https://votre-api.com/api/waitlist'` par l'URL de votre endpoint API.

### 2. Format des données envoyées

Le formulaire envoie un objet JSON avec les champs suivants :

```json
{
  "name": "Prénom de l'utilisateur",
  "email": "email@example.com",
  "company": "Nom du garage (optionnel, peut être null)",
  "timestamp": "2024-01-01T12:00:00.000Z"
}
```

### 3. Réponse attendue de l'API

Votre API doit :
- Accepter les requêtes POST avec `Content-Type: application/json`
- Retourner un statut HTTP 200 (OK) en cas de succès
- Retourner une réponse JSON (le contenu exact n'est pas critique)

Exemple de réponse réussie :
```json
{
  "success": true,
  "message": "Utilisateur ajouté à la waitlist",
  "id": "123"
}
```

## 🛠️ Exemple d'implémentation backend

### Node.js + Express + Base de données

```javascript
const express = require('express');
const cors = require('cors');
const app = express();

// Middleware
app.use(cors());
app.use(express.json());

// Endpoint waitlist
app.post('/api/waitlist', async (req, res) => {
  try {
    const { name, email, company, timestamp } = req.body;

    // Validation
    if (!name || !email) {
      return res.status(400).json({
        error: 'Les champs name et email sont obligatoires'
      });
    }

    // Sauvegarder dans votre base de données
    // Exemple avec PostgreSQL :
    // const result = await db.query(
    //   'INSERT INTO waitlist (name, email, company, created_at) VALUES ($1, $2, $3, $4) RETURNING *',
    //   [name, email, company, timestamp]
    // );

    // Ou avec MongoDB :
    // const newEntry = await Waitlist.create({ name, email, company, timestamp });

    res.status(200).json({
      success: true,
      message: 'Inscription réussie'
    });

  } catch (error) {
    console.error('Erreur:', error);
    res.status(500).json({
      error: 'Erreur serveur'
    });
  }
});

app.listen(3000, () => {
  console.log('API démarrée sur le port 3000');
});
```

## 🔒 CORS (Cross-Origin Resource Sharing)

Si votre API est sur un domaine différent de votre landing page, vous devrez configurer CORS :

```javascript
// Node.js/Express
app.use(cors({
  origin: 'https://votre-domaine.com',
  methods: ['POST']
}));
```

## 🧪 Test de l'API

Pour tester votre endpoint API avant de le connecter au formulaire :

```bash
curl -X POST https://votre-api.com/api/waitlist \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Test User",
    "email": "test@example.com",
    "company": "Test Garage",
    "timestamp": "2024-01-01T12:00:00.000Z"
  }'
```

## 📝 Structure de base de données suggérée

### SQL (PostgreSQL, MySQL)

```sql
CREATE TABLE waitlist (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) NOT NULL UNIQUE,
  company VARCHAR(255),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### NoSQL (MongoDB)

```javascript
const waitlistSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  company: { type: String, default: null },
  timestamp: { type: Date, default: Date.now }
});
```

## 🚨 Gestion des erreurs

Le formulaire gère automatiquement :
- ✅ Validation des champs obligatoires
- ✅ Validation du format email
- ✅ État de chargement pendant l'envoi
- ✅ Messages d'erreur en cas d'échec
- ✅ Affichage du message de succès

Si l'API retourne une erreur ou est inaccessible, l'utilisateur verra un message d'erreur et pourra réessayer.

## 📧 Contact

Pour toute question : hello@snapcar.app
