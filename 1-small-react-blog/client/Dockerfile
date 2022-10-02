FROM node:alpine

ENV CI=true

WORKDIR /app

COPY ./package.json ./
RUN npm install
RUN npm install axios
COPY ./ ./

CMD ["npm","start"]