FROM alpine:3.24
#
# Timezone
#
ENV TZ=Pacific/Tahiti
RUN apk add --no-cache ca-certificates curl tzdata wget \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo "$TZ" > /etc/timezone
#
# Locale
#
ENV LANG=fr_FR:UTF-8
ENV LANGUAGE=fr
ENV LC_CTYPE=fr_FR.utf8
ENV LC_MESSAGES=fr_FR.utf8
ENV LC_ALL=fr_FR.utf8
ENV MUSL_LOCPATH=/usr/share/i18n/locales/musl
# libintl = dépendance d'exécution (support gettext) → reste dans l'image.
# Les outils de compilation sont installés en paquet virtuel puis supprimés.
# NB : master.zip est une référence mobile (pas de checksum). Pour un build
# reproductible et intègre, épingler un tag/commit de govpf/musl-locales.
RUN apk add --no-cache libintl \
    && apk add --no-cache --virtual .musl-locale-deps cmake make musl-dev gcc gettext-dev \
    && wget https://github.com/govpf/musl-locales/archive/master.zip \
    && unzip master.zip \
    && cd musl-locales-master \
    # Alpine 3.24 fournit CMake 4, qui refuse les projets déclarant
    # cmake_minimum_required < 3.5. Le CMakeLists de musl-locales est concerné,
    # d'où le flag ci-dessous. Fix propre : bumper cmake_minimum_required dans le fork.
    && cmake -DCMAKE_POLICY_VERSION_MINIMUM=3.5 -DLOCALE_PROFILE=OFF -D CMAKE_INSTALL_PREFIX:PATH=/usr . && make && make install \
    && cd .. && rm -rf musl-locales-master master.zip \
    && apk del .musl-locale-deps
#
# Certs
#
COPY install_certs /tmp/install_certs
RUN apk add --no-cache bash && bash -xe /tmp/install_certs && rm -f /tmp/install_certs
# OS upgrade
RUN apk update && apk upgrade --no-cache
 