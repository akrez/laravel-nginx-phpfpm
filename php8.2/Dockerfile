FROM ubuntu:24.04

LABEL maintainer="Taylor Otwell"

ARG WWWGROUP
ARG NODE_VERSION=24
ARG POSTGRES_VERSION=18

WORKDIR /var/www/html

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN echo "Acquire::http::Pipeline-Depth 0;" > /etc/apt/apt.conf.d/99custom &&     echo "Acquire::http::No-Cache true;" >> /etc/apt/apt.conf.d/99custom &&     echo "Acquire::BrokenProxy    true;" >> /etc/apt/apt.conf.d/99custom

RUN apt-get update && apt-get upgrade -y     && mkdir -p /etc/apt/keyrings     && apt-get install -y gnupg gosu curl ca-certificates zip unzip git        sqlite3 libcap2-bin libpng-dev python3 dnsutils librsvg2-bin fswatch ffmpeg nano     && curl -sS 'https://keyserver.ubuntu.com/pks/lookup?op=get&search=0xb8dc7e53946656efbce4c1dd71daeaab4ad4cab6'        | gpg --dearmor | tee /etc/apt/keyrings/ppa_ondrej_php.gpg > /dev/null     && echo "deb [signed-by=/etc/apt/keyrings/ppa_ondrej_php.gpg] https://ppa.launchpadcontent.net/ondrej/php/ubuntu noble main"        > /etc/apt/sources.list.d/ppa_ondrej_php.list     && apt-get update     && apt-get install -y        php8.2-fpm php8.2-cli php8.2-dev        php8.2-pgsql php8.2-sqlite3 php8.2-gd php8.2-imagick        php8.2-curl php8.2-mongodb php8.2-imap php8.2-mysql        php8.2-mbstring php8.2-xml php8.2-zip php8.2-bcmath        php8.2-soap php8.2-intl php8.2-readline php8.2-ldap        php8.2-msgpack php8.2-igbinary php8.2-redis php8.2-swoole        php8.2-memcached php8.2-pcov php8.2-xdebug     && curl -sLS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer     && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key        | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg     && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_VERSION.x nodistro main"        > /etc/apt/sources.list.d/nodesource.list     && apt-get update     && apt-get install -y nodejs     && npm install -g npm pnpm bun     && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg        | gpg --dearmor | tee /etc/apt/keyrings/yarn.gpg >/dev/null     && echo "deb [signed-by=/etc/apt/keyrings/yarn.gpg] https://dl.yarnpkg.com/debian/ stable main"        > /etc/apt/sources.list.d/yarn.list     && curl -sS https://www.postgresql.org/media/keys/ACCC4CF8.asc        | gpg --dearmor | tee /etc/apt/keyrings/pgdg.gpg >/dev/null     && echo "deb [signed-by=/etc/apt/keyrings/pgdg.gpg] http://apt.postgresql.org/pub/repos/apt noble-pgdg main"        > /etc/apt/sources.list.d/pgdg.list     && apt-get update     && apt-get install -y yarn mysql-client postgresql-client-$POSTGRES_VERSION     && apt-get autoremove -y     && apt-get clean     && rm -rf /var/lib/apt/lists/*

RUN setcap "cap_net_bind_service=+ep" /usr/bin/php8.2

RUN groupadd --force -g $WWWGROUP sail  && useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 sail

RUN git config --global --add safe.directory /var/www/html

COPY php.ini /etc/php/8.2/fpm/conf.d/99-sail.ini

EXPOSE 9000

COPY fpm-pool.conf /etc/php/8.2/fpm/pool.d/www.conf

CMD ["php-fpm8.2", "-F"]
