FROM ubuntu:latest AS builder

RUN apt-get update && apt-get install curl ca-certificates -y

# Install HUGO
ENV HUGO_VERSION="0.101.0"

WORKDIR /usr/bin
RUN curl -L "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_VERSION}_Linux-64bit.tar.gz" -o "hugo.tar.gz" && \
    tar xvzf hugo.tar.gz

RUN chmod +x /usr/bin/hugo

# Generate static files
COPY . /app

WORKDIR /app
RUN hugo --minify

# base image
FROM nginx:latest AS server

# copy artifact build from the 'build environment'
COPY --from=builder /app/public /usr/share/nginx/html

# add NGINX config
COPY nginx/default.conf /etc/nginx/conf.d/default.conf
RUN chown -R nginx /etc/nginx /var/run /run
RUN chmod -R a+w /var/run /run /var/cache /var/cache/nginx

# expose port 80
EXPOSE 80

# run nginx
CMD ["nginx", "-g", "daemon off;"]