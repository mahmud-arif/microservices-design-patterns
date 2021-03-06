FROM node:10.15.3-slim as builder

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN apt-get update && apt-get install netcat-openbsd -y

RUN npm install --silent

# Bundle app source
COPY . .

RUN sed -i "s#baseHref: '/'#baseHref: '/jhipster/'#g" ./webpack/webpack.common.js

RUN npm run webpack:prod --silent

### STAGE 2: Production Environment ###
FROM nginx:1.13.12-alpine

ARG PORT
ARG SERVER_PROTOCOL
ARG SERVER_URL

ENV PORT=$PORT
ENV SERVER_PROTOCOL=$SERVER_PROTOCOL
ENV SERVER_URL=$SERVER_URL

COPY --from=builder /usr/src/app/build/resources/main/static /usr/share/nginx/html
COPY --from=builder /usr/src/app/nginx.conf /etc/nginx/conf.d/default.conf

RUN echo "PORT = $PORT"

RUN echo "SERVER_PROTOCOL = $SERVER_PROTOCOL"

RUN echo "SERVER_URL = $SERVER_URL"

RUN sed -i 's/PORT/'"$PORT"'/g' /etc/nginx/conf.d/default.conf

RUN sed -i 's/SERVER_PROTOCOL/'"$SERVER_PROTOCOL"'/g' /etc/nginx/conf.d/default.conf

RUN sed -i 's/SERVER_URL/'"$SERVER_URL"'/g' /etc/nginx/conf.d/default.conf

RUN echo "daemon off;" >> /etc/nginx/nginx.conf

EXPOSE $PORT

ENV JAVA_CMD="nginx"
CMD ["nginx"]
