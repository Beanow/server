FROM nginx:1.15-alpine

LABEL com.bitwarden.product="bitwarden"

COPY nginx.conf /etc/nginx
COPY proxy.conf /etc/nginx
COPY mime.types /etc/nginx
COPY security-headers.conf /etc/nginx

EXPOSE 8080
