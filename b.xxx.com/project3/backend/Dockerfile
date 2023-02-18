# syntax=docker/dockerfile:1.4
FROM node:lts

ARG NODE_ENV=production
ENV NODE_ENV $NODE_ENV

WORKDIR /app

ENV PORT=8001

COPY package.json /app/package.json
COPY package-lock.json /app/package-lock.json
RUN npm ci

COPY . /app
CMD [ "node", "src/index.js" ]
