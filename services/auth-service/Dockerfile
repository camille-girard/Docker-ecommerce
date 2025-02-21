# Étape 1 : Développement sécurisé
FROM node:18-alpine AS development

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY package.json ./

RUN npm install && npm cache clean --force \
    && chown -R appuser:appgroup /app

COPY . .

USER appuser

EXPOSE 3001

CMD ["npm", "run", "dev"]

FROM node:18-alpine AS builder

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY package.json ./

RUN npm install --omit=dev && npm cache clean --force \
    && chown -R appuser:appgroup /app

COPY . .

USER appuser

FROM node:18-alpine AS production

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY --from=builder --chown=appuser:appgroup /app /app

ENV NODE_ENV=production
ENV MONGODB_URI=mongodb://mongodb:27017/order-service
ENV JWT_SECRET_FILE=/run/secrets/jwt_secret

USER appuser

EXPOSE 3001

CMD ["npm", "start"]
