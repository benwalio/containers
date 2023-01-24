FROM node:12-alpine

ARG TARGETPLATFORM
ARG VERSION
ARG CHANNEL

WORKDIR /tmp

RUN apk add --no-cache \
  bash \
  curl \
  gnupg

RUN curl -fsSL "https://github.com/thedevs-network/kutt/archive/refs/tags/v${VERSION}.tar.gz" \
        | tar xzf - -C .

WORKDIR /tmp/kutt-${VERSION}

RUN rm ./LICENSE ./README.md \
    && mkdir /app \
    && cp -r ./* /app \
    && chown -R root:root /app \
    && chmod -R 755 /app \
    && rm -rf /tmp/*

# Setting working directory. 
WORKDIR /app

# Installing dependencies
RUN npm install

# Copying source files
COPY . .

# Give permission to run script
RUN chmod +x ./wait-for-it.sh

# Build files
RUN npm run build

EXPOSE 3000

# Running the app
CMD [ "npm", "start" ]