FROM nginx:1.31-alpine
# Install prerequisites required for tools and extensions installed later on.
# RUN apk add --update bash nano su-exec mc

ADD Dockerfile/nginx/default.conf /etc/nginx/conf.d

# Setting the work directory.
WORKDIR /var/www/html/public