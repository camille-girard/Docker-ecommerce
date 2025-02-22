FROM node:18-alpine AS dev-stage

WORKDIR /app

COPY package.json ./

RUN npm install && npm cache clean --force

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev"]

FROM node:18-alpine AS build-stage

WORKDIR /app

COPY package.json ./

RUN npm install && npm cache clean --force

COPY . .

RUN npm run build

FROM nginx:alpine AS production-stage

COPY --from=build-stage /app/dist /usr/share/nginx/html

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]