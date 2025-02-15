FROM node:18-alpine AS development

WORKDIR /app

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 3001

CMD ["npm", "run", "dev"]

FROM node:18-alpine AS builder

WORKDIR /app

COPY package.json ./

RUN npm install --omit=dev

COPY . .

FROM node:18-alpine AS production

WORKDIR /app

COPY --from=builder /app /app

ENV NODE_ENV=production

EXPOSE 3001

CMD ["npm", "start"]
