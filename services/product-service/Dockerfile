FROM node:18-alpine AS base

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY package.json ./

FROM base AS development

RUN npm install && npm cache clean --force \
    && chown -R appuser:appgroup /app

COPY . .

USER appuser

ENV MONGODB_URI=mongodb://mongodb:27017/mongodb

EXPOSE 3000

CMD ["npm", "run", "dev"]

FROM base AS builder

RUN npm install && npm cache clean --force \
    && chown -R appuser:appgroup /app

COPY . .

RUN npm run build || echo "No build script found, skipping build."

USER appuser

FROM node:18-alpine AS production

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY --from=builder --chown=appuser:appgroup /app /app

ENV NODE_ENV=production

USER appuser

EXPOSE 3000

CMD ["npm", "run", "start"]
