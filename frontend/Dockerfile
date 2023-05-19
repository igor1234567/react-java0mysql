FROM node:lts AS development

WORKDIR /code
COPY frontend/package.json /code/package.json
COPY frontend/package-lock.json /code/package-lock.json

RUN npm ci

COPY . /code

ENV CI=true
ENV PORT=3000

ENTRYPOINT [ "npm", "start" ]
