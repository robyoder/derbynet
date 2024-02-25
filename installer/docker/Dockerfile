# To build:
# docker build -t derbynet_server -f installer/docker/Dockerfile .

# To run in development mode:
# 1. Create the directory where you want uploaded files (?) to live, such as ~/Public/derbynet
# 2. Run the following command, replacing `~/Public/derbynet` with the directory of your choosing:
# 
# docker run -d -p 80:80 -p 443:443 \
#     -v ~/Public/derbynet:/var/lib/derbynet \
#     -v /etc/localtime:/etc/localtime:ro \
#     -v $(pwd)/website/:/var/www/html:ro \
#     derbynet_server
# 
# If you aren't actively changing the site code, you can leave out the third `-v` line above,
# and the container will use a static copy of the site files.

FROM alpine
LABEL org.opencontainers.image.authors=jeffpiazza
LABEL description="DerbyNet is the new standard in race management software for Pinewood Derby events.  \
Visit http://jeffpiazza.github.io/derbynet/ for more information about DerbyNet."

# I don't know when PHP picked up a dependency on tzdata and requires it be
# configured, but that seems to be the case now.  Eastern time is convenient for
# me, but may not be the best choice for everyone.
ENV TZ=America/New_York
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apk update && \
    apk upgrade && \
    apk add bash && \
    apk add nginx && \
    apk add php && \
    apk add php-fpm && \
    apk add php-opcache && \
    apk add php-gd && \
    apk add php-curl && \
    apk add php-iconv && \
    apk add php-session && \
    apk add php-pdo && \
    apk add php-pdo_sqlite && \
    apk add php-pdo_odbc && \
    apk add php-pdo_mysql

# This Dockerfile and the companion alpine-configure-nginx.sh assume we're using
# PHP 8.2.  If that changes, it's time to update.
RUN ls /usr/sbin/php-fpm82 || exit 1

ARG WWW_ROOT=/var/www/html

COPY website/ $WWW_ROOT

RUN rm -rf $WWW_ROOT/local/* $WWW_ROOT/index*.html && \
    ( test -d $WWW_ROOT/local || mkdir -m 777 $WWW_ROOT/local ) && \
    mkdir -m 777 /var/lib/derbynet && \
    mkdir -m 777 /var/lib/derbynet/imagery && \
    mkdir -m 777 /var/lib/derbynet/slides && \
    mkdir /var/run/php

# The set-timeout.sh script may be needed to prevent timeouts under automated testing.
# Under "normal" production use, stock timeouts are probably OK.
COPY installer/docker/alpine-* installer/docker/set-timeout.sh /usr/local/bin/

# How large are the pictures you want to upload?  8M covers an 18-megapixel
# photo.
ENV MAX_UPLOAD_SIZE=16M

RUN bash /usr/local/bin/alpine-snakeoil.sh && \
    bash /usr/local/bin/alpine-configure-nginx.sh

VOLUME /var/lib/derbynet

EXPOSE 80 443
STOPSIGNAL SIGTERM

CMD ["/bin/bash", "-c", "php-fpm82 && chmod 777 /var/run/php/php-fpm.sock && nginx -g 'daemon off;'"]
