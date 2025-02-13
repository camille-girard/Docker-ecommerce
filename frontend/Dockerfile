# Étape 1: Build du frontend Vue.js
FROM node:18-alpine AS build-stage

# Définir le répertoire de travail dans le conteneur
WORKDIR /app

# Copier les fichiers package.json et package-lock.json (si existant)
COPY package.json ./

# Installer les dépendances
RUN npm install

# Copier le reste de l'application
COPY . .

# Build de l'application Vue.js
RUN npm run build

# Étape 2: Image finale avec Nginx
FROM nginx:alpine AS production-stage

# Copier les fichiers build dans le dossier de Nginx
COPY --from=build-stage /app/dist /usr/share/nginx/html

# Supprimer la configuration par défaut de Nginx
RUN rm /etc/nginx/conf.d/default.conf

# Ajouter une nouvelle configuration Nginx pour Vue.js
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Exposer le port 80
EXPOSE 80

# Lancer Nginx en mode foreground
CMD ["nginx", "-g", "daemon off;"]
