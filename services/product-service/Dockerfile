FROM node:22-alpine AS base
WORKDIR /app
COPY package*.json ./

FROM base AS development
RUN npm install
COPY . .
ENV MONGODB_URI=mongodb://mongodb:27017/mongodb
EXPOSE 3000
CMD ["npm", "run", "dev"]

FROM base AS builder
RUN npm install
COPY . .
RUN npm run build || echo "No build script found, skipping build."

FROM node:22-alpine AS production
WORKDIR /app
COPY package*.json ./
RUN npm install --only=production
EXPOSE 3000
CMD ["npm", "run", "start"]
