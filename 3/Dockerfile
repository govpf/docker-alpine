FROM alpine:3.16

#
# Timezone
#

ENV TZ Pacific/Tahiti
RUN apk add --no-cache ca-certificates curl tzdata wget \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo "$TZ" > /etc/timezone

#
# Locale
#

ENV LANG fr_FR:UTF-8
ENV LANGUAGE fr
ENV LC_CTYPE fr_FR.utf8
ENV LC_MESSAGES fr_FR.utf8
ENV LC_ALL fr_FR.utf8

ENV MUSL_LOCALE_DEPS cmake make musl-dev gcc gettext-dev libintl
ENV MUSL_LOCPATH /usr/share/i18n/locales/musl
RUN apk add --no-cache $MUSL_LOCALE_DEPS \
    && wget https://github.com/govpf/musl-locales/archive/master.zip \
    && unzip master.zip \
    && cd musl-locales-master \
    && cmake -DLOCALE_PROFILE=OFF -D CMAKE_INSTALL_PREFIX:PATH=/usr . && make && make install \
    && cd .. && rm -r musl-locales-master


#
# Certs
#

COPY install_certs /tmp/install_certs
RUN  apk add --no-cache bash openssl && bash -xe /tmp/install_certs

# OS upgrade 

RUN apk update && apk upgrade --no-cache
