FROM hyperf/hyperf:7.2-alpine-base

LABEL maintainer="jksusu" version="1.0" license="MIT"

##
# ---------- env settings ----------
##
ENV SWOOLE_VERSION=4.2.11 \
     MSGPACK=2.1.1 \
     YAR=2.0.5 \
    #  install and remove building packages
     PHPIZE_DEPS=" curl-dev autoconf dpkg-dev dpkg file g++ gcc libc-dev make php7-dev php7-pear pkgconf re2c pcre-dev zlib-dev libtool automake" \
    # update
RUN set -ex \
        && apk update \
        && apk add --no-cache libstdc++ openssl  bash \
        && apk add --no-cache --virtual .build-deps $PHPIZE_DEPS libaio-dev openssl-dev \
        && cd /tmp \
        && curl -SL "https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz" -o swoole.tar.gz \
        && ls -alh \
        && cd /tmp \
        && mkdir -p swoole \
        && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
        && ( \
            cd swoole \
            && phpize \
            && ./configure --enable-mysqlnd --enable-openssl \
            && make -s -j$(nproc) && make install \
        ) \
        && echo "extension=swoole.so" > /etc/php7/conf.d/swoole.ini \
        && php --ri swoole \

        && cd /tmp \
        && curl -SL "https://codeload.github.com/msgpack/msgpack-php/tar.gz/msgpack-${MSGPACK}" -o msgpack.tar.gz \
        && ls -alh \
        && cd /tmp \
        && mkdir -p msgpack \
        && tar -xf msgpack.tar.gz -C msgpack --strip-components=1 \
        && ( \
            cd msgpack \
            && phpize \
            && ./configure \
            && make -s -j$(nproc) && make install \
        ) \
        && echo "extension=msgpack.so" > /etc/php7/conf.d/msgpack.ini \
        && php --ri msgpack \

        && cd /tmp \
        && curl -SL "https://codeload.github.com/laruence/yar/tar.gz/yar-${YAR}" -o yar.tar.gz \
        && ls -alh \
        && cd /tmp \
        && mkdir -p yar \
        && tar -xf yar.tar.gz -C yar --strip-components=1 \
        && ( \
            cd yar \
            && phpize \
            && ./configure --enable-msgpack=yes --with-curl \
            && make -s -j$(nproc) && make install \
        ) \
        && echo "extension=yar.so" > /etc/php7/conf.d/yar.ini \
        && php --ri yar \

        && apk del .build-deps \
        && rm -rf /var/cache/apk/* /tmp/* /usr/share/man \
        && echo -e "\033[42;37m Build Completed :).\033[0m\n"