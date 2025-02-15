FROM node:22-alpine AS builder

WORKDIR /app

COPY package.json ./

RUN npm i

COPY . .

FROM builder AS production
ENV NODE_ENV=production
RUN npm run build

FROM builder AS development
ENV NODE_ENV=development

CMD ["npm", "run", "dev"]

FROM ${NODE_ENV:-development}
EXPOSE 3000
CMD ["npm", "run", "start"]
