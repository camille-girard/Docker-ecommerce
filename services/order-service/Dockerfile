# Étape 1 : Build de l'application
FROM node:18-alpine AS builder

WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev
COPY . .

# Étape 2 : Image finale minimale et sécurisée pour la production
FROM node:18-alpine AS production

# Créer un utilisateur non-root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

# Copier le contenu depuis l'étape builder
COPY --from=builder /app .

# Variables d'environnement pour production
ENV NODE_ENV=production
ENV PORT=3002
ENV MONGODB_URI=mongodb://mongodb:27017/order-service
ENV JWT_SECRET_FILE=/run/secrets/jwt_secret
ENV VITE_PRODUCT_SERVICE_URL=http://product-service:3000

EXPOSE 3002

# Passer à un utilisateur non-root
RUN chown -R appuser:appgroup /app
USER appuser

CMD ["node", "src/app.js"]

# Étape 3 : Mode Développement avec hot-reloading
FROM node:18-alpine AS development

WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
COPY . .

# Variables d'environnement pour le développement
ENV NODE_ENV=development
ENV PORT=3002
ENV MONGODB_URI=mongodb://mongodb:27017/order-service
ENV VITE_PRODUCT_SERVICE_URL=http://localhost:3000

EXPOSE 3002

CMD ["npm", "run", "dev"]
