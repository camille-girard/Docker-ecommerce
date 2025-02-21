FROM node:18-alpine AS dev-stage

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY package.json ./

RUN npm install && npm cache clean --force \
    && chown -R appuser:appgroup /app

COPY . .

USER appuser

EXPOSE 5173

CMD ["npm", "run", "dev"]

FROM node:18-alpine AS build-stage

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

COPY package.json ./

RUN npm install --only=production && npm cache clean --force && npm run build \
    && chown -R appuser:appgroup /app

COPY . .

USER appuser

FROM nginx:alpine AS production-stage

RUN addgroup -S nginxgroup && adduser -S nginxuser -G nginxgroup \
    && chown -R nginxuser:nginxgroup /usr/share/nginx/html

WORKDIR /usr/share/nginx/html

COPY --from=build-stage /app/dist /usr/share/nginx/html

RUN rm /etc/nginx/conf.d/default.conf \
    && chown -R nginxuser:nginxgroup /etc/nginx/conf.d

COPY nginx.conf /etc/nginx/conf.d/default.conf

USER nginxuser

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
