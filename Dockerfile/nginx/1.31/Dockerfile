ARG NGINX_VERSION=1.31
ARG BUILD_PLATFORM=alpine

FROM nginx:${NGINX_VERSION}-${BUILD_PLATFORM}
# Install prerequisites required for tools and extensions installed later on.
# RUN apk add --update bash nano su-exec mc

LABEL maintainer="Carsten Hölbing <carsten.hoelbing@eah-jena.de>"
LABEL description="Nginx for postbuch"

ADD default.conf /etc/nginx/conf.d

# Setting the work directory.
WORKDIR /var/www/html/public