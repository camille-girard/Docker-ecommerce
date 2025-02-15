FROM node:18-alpine AS builder

WORKDIR /app
COPY package.json ./
RUN npm ci --omit=dev
COPY . .

FROM node:18-alpine AS production

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY --from=builder /app .

ENV NODE_ENV=production
ENV PORT=3002
ENV MONGODB_URI=mongodb://mongodb:27017/order-service
ENV JWT_SECRET_FILE=/run/secrets/jwt_secret
ENV VITE_PRODUCT_SERVICE_URL=http://product-service:3000

EXPOSE 3002

RUN chown -R appuser:appgroup /app
USER appuser

CMD ["node", "src/app.js"]

FROM node:18-alpine AS development

WORKDIR /app
COPY package.json ./
RUN npm install
COPY . .

ENV NODE_ENV=development
ENV PORT=3002
ENV MONGODB_URI=mongodb://mongodb:27017/order-service
ENV VITE_PRODUCT_SERVICE_URL=http://localhost:3000

EXPOSE 3002

CMD ["npm", "run", "dev"]
