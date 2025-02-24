# Projet E-Commerce Microservices

## 📝 Description du Projet

Ce projet e-commerce utilise une architecture microservices comprenant un frontend en **Vue.js** et trois services backend (**Auth Service**, **Product Service**, **Order Service**), tous développés en **Node.js** et **Express**, avec **MongoDB**.

---

## ⚙️ **Étapes pour Construire et Exécuter les Conteneurs**

### 🐳 **Prérequis :**

- Docker & Docker Compose installés.
- Fichiers `.env.development` et `.env.production` configurés (**.env.production** est protégé via `.gitignore`).

### 🚀 **1. Environnement de Développement**

Pour démarrer les services en mode développement, utilisez la commande suivante :

```bash
docker compose -f compose.yml up --build -d
```
- docker compose: Utilise Docker Compose pour gérer les conteneurs.
- -f compose.yml: Spécifie le fichier de configuration Docker Compose à utiliser (compose.yml).
- up: Crée et démarre les conteneurs définis dans le fichier de configuration.
- --build: Reconstruit les images Docker avant de démarrer les conteneurs.
- -d: Démarre les conteneurs en mode détaché (en arrière-plan).

Les services seront accessibles aux adresses suivantes :
- Frontend : `http://localhost:8090`
- Auth Service : `http://localhost:3001`
- Product Service : `http://localhost:3000`
- Order Service : `http://localhost:3002`
- MongoDB : `mongodb://localhost:27017`

### 🌐 **2. Environnement de Production**

Pour démarrer les services en mode production, utilisez la commande suivante :

```bash
docker compose -f compose.prod.yml up -d
```

Les services seront accessibles aux adresses suivantes :
- **Frontend (via Nginx)** : L'application frontend est servie par Nginx sur `http://localhost:8090`, offrant des performances améliorées et une meilleure gestion des requêtes HTTP.
- **Déploiement répliqué** : Chaque service backend (Auth, Product, Order) s'exécute en 2 instances, garantissant la haute disponibilité et la scalabilité. MongoDB fonctionne en mode global, assurant qu'une seule instance s'exécute par nœud Docker.
- **Vérification de la santé des services** : Docker effectue des vérifications automatiques (healthchecks) en envoyant régulièrement des requêtes aux points `/api/health`. Si un service échoue, Docker le redémarre automatiquement.

### 🛑 **3. Arrêt des Services :**

Pour arrêter et supprimer les conteneurs, les réseaux, volumes et images, utilisez la commande suivante :

```bash
docker-compose down
```

### 🔄 **4. Nettoyage :**

Pour nettoyer les ressources Docker inutilisées, utilisez la commande suivante.
Cette commande supprime toutes les ressources inutilisées (conteneurs, réseaux, images, volumes) pour libérer de l'espace :
```bash
docker system prune -f
```

---

## 🌍 **Configurations Spécifiques à Chaque Environnement**

### 🧪 **Développement :**

- Rechargement à chaud pour le frontend avec `npm run dev`.
- Accès MongoDB sans authentification.
- Variables d'environnement chargées via `.env.development`.

### 🚀 **Production :**

- Nginx pour servir le frontend.
- MongoDB sécurisé avec utilisateur `root` et mot de passe `example`.
- Variables d'environnement chargées via `.env.production` (non versionné).

---

## 🧪 **Commandes pour Tester les Services**

### 🔗 **Santé des Services :**

Pour vérifier la santé des services, utilisez les commandes suivantes :

```bash
curl http://localhost:3001/api/health  # Auth Service
curl http://localhost:3000/api/health  # Product Service
curl http://localhost:3002/api/health  # Order Service
```

### 🔐 **Tester les Services :**

#### 🧪 **Tests Réalisés :**

Le projet a été testé en incluant les tests suivants :

- **Tests fournis par Monsieur Laine** : Tous les tests cURL, les scripts `run-tests.sh` et les commandes `npm` fournis ont été exécutés avec succès.
- **Tests de Sécurité avec Trivy** :
  - Analyse des images Docker pour détecter les vulnérabilités.
  - Commandes utilisées :
    ```bash
    trivy fs . 
    trivy image docker-ecommerce-auth-service:latest
    trivy image docker-ecommerce-product-service:latest
    trivy image docker-ecommerce-order-service:latest
    trivy image docker-ecommerce-frontend:latest
    ```

- **Tests d'Interaction entre les Services** :

#### 🔄 **Tests d'Interaction entre les Services**

#### Inscritpion d'un Utilisateur (si cela n'a pas déjà été fait)

```bash
curl -X POST http://localhost:3001/api/auth/register \
-H "Content-Type: application/json" \
-d '{"email": "user@example.com", "password": "password123"}'

echo "Utilisateur inscrit avec succès"
```

##### 1️⃣ **Authentification (récupération du token JWT)**

```bash
TOKEN=$(curl -s -X POST http://localhost:3001/api/auth/login \
-H "Content-Type: application/json" \
-d '{"email": "user@example.com", "password": "password123"}' | jq -r '.token')

echo "Token récupéré : $TOKEN"
```

##### 2️⃣ **Récupération de la liste des produits (Product Service)**

```bash
PRODUCT_ID=$(curl -s http://localhost:3000/api/products | jq -r '.[0]._id')

echo "Produit récupéré - ID : $PRODUCT_ID"
```

##### 3️⃣ **Création d'une commande (Order Service)**

```bash
curl -X POST http://localhost:3002/api/orders \
-H "Authorization: Bearer $TOKEN" \
-H "Content-Type: application/json" \
-d "{\"products\":[{\"productId\":\"$PRODUCT_ID\",\"quantity\":1}],\"shippingAddress\":{\"street\":\"123 Rue Exemple\",\"city\":\"Paris\",\"postalCode\":\"75000\"}}"
echo "Commande créée avec succès"
```

##### 4️⃣ **Consultation de l’historique des commandes**

```bash
curl -H "Authorization: Bearer $TOKEN" http://localhost:3002/api/orders
echo "Historique des commandes récupéré"
```

##### 5️⃣ **Scénarios d'échec pour tester la robustesse :**

- **Commande sans authentification :**

```bash
curl -X POST http://localhost:3002/api/orders \
-H "Content-Type: application/json" \
-d "{\"products\":[{\"productId\":\"$PRODUCT_ID\",\"quantity\":1}],\"shippingAddress\":{\"street\":\"123 Rue Exemple\",\"city\":\"Paris\",\"postalCode\":\"75000\"}}"
```

- **Commande avec produit inexistant :**

```bash
curl -X POST http://localhost:3002/api/orders \
-H "Authorization: Bearer $TOKEN" \
-H "Content-Type: application/json" \
-d "{\"products\":[{\"productId\":\"invalid_id\",\"quantity\":1}],\"shippingAddress\":{\"street\":\"123 Rue Exemple\",\"city\":\"Paris\",\"postalCode\":\"75000\"}}"
```

##### 🎯 **Ce que ces tests valident :**

1. 🔐 **L'authentification** avec le service d'auth (JWT valide).
2. 🛒 **La récupération des produits** depuis le Product Service.
3. 📝 **La création d'une commande** dans l'Order Service en utilisant les données du Product Service.
4. 📦 **La consultation de l’historique des commandes**, prouvant que la commande a bien été enregistrée.
5. ⚡ **La gestion des erreurs**, garantissant que les dépendances interservices sont bien gérées.

---

## 🏆 **Bonnes Pratiques Appliquées**

- **Sécurité** : JWT pour l'authentification, `.env.production` ignoré dans Git, execution des containers en tant qu'utilisateur non-root.
- **Optimisation Docker** : Construction multi-étapes pour des images légères, `.dockerignore` pour chaque service, supression des fichiers inutiles, reduction de la taille des images.
- **Scalabilité** : Déploiement répliqué en production.
- **Architecture modulaire** : Microservices indépendants.

---

## **Conclusion**

Ce projet e-commerce propose une architecture robuste, sécurisée et scalable, avec une configuration Docker optimisée pour le développement et la production.

