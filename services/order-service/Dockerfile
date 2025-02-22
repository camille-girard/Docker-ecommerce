FROM node:18-alpine AS development

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY package.json ./

RUN npm install && npm cache clean --force \
    && chown -R appuser:appgroup /app

COPY . .

USER appuser

ENV NODE_ENV=development
ENV PORT=3002
ENV MONGODB_URI=mongodb://mongodb:27017/order-service
ENV VITE_PRODUCT_SERVICE_URL=http://localhost:3000

EXPOSE 3002

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

COPY --from=builder --chown=appuser:appgroup /app .

ENV NODE_ENV=production
ENV PORT=3002
ENV MONGODB_URI=mongodb://mongodb:27017/order-service
ENV JWT_SECRET_FILE=/run/secrets/jwt_secret
ENV VITE_PRODUCT_SERVICE_URL=http://product-service:3000

USER appuser

EXPOSE 3002

CMD ["node", "src/app.js"]
