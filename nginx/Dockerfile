FROM nginx:stable-alpine

LABEL maintainer=tronglt

RUN apk update
RUN apk add openssl

# Install OpenSSL and create certificate files for https configuration.
COPY ./ssl.conf /etc/ssl/ssl.conf
RUN openssl req -x509 -out /etc/ssl/localhost.crt -keyout /etc/ssl/localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config /etc/ssl/ssl.conf
