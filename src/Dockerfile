FROM php:cli-alpine3.14

RUN	addgroup -S adminer \
    && adduser -S -G adminer adminer \
    && mkdir -p /var/adminer \
    && chown -R adminer:adminer /var/adminer \
    && apk add --no-cache --virtual .persistent-deps freetds unixodbc postgresql-dev sqlite-dev curl  \
    && apk add --no-cache --virtual .build-deps freetds-dev unixodbc-dev autoconf g++ make gnupg \
    && docker-php-source extract \
    && docker-php-ext-configure pdo_odbc --with-pdo-odbc=unixODBC,/usr \
    && docker-php-ext-install opcache \
    && docker-php-ext-install pdo \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install pdo_odbc \
    && docker-php-ext-install pdo_pgsql \
    && docker-php-ext-install pdo_sqlite \
    && docker-php-ext-install pdo_dblib \
    && docker-php-ext-install session \
    && pecl install sqlsrv pdo_sqlsrv \
    && docker-php-ext-enable --ini-name 30-sqlsrv.ini sqlsrv \
    && docker-php-ext-enable --ini-name 35-pdo_sqlsrv.ini pdo_sqlsrv \
    && curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.8.1.1-1_amd64.apk \
    && curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/mssql-tools_17.8.1.1-1_amd64.apk \
    && curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.8.1.1-1_amd64.sig \
    && curl -O https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/mssql-tools_17.8.1.1-1_amd64.sig \
    && curl https://packages.microsoft.com/keys/microsoft.asc  | gpg --import - \
    && gpg --verify msodbcsql17_17.8.1.1-1_amd64.sig msodbcsql17_17.8.1.1-1_amd64.apk \
    && gpg --verify mssql-tools_17.8.1.1-1_amd64.sig mssql-tools_17.8.1.1-1_amd64.apk \
    && apk add --allow-untrusted msodbcsql17_17.8.1.1-1_amd64.apk \
    && apk add --allow-untrusted mssql-tools_17.8.1.1-1_amd64.apk \
    && docker-php-source delete \
    && apk del .build-deps

WORKDIR /var/adminer

ARG ADMINER_VERSION=4.8.1
ARG ADMINER_FLAVOUR="-en"
ARG ADMINER_CHECKSUM="4ec36be619bb571e2b5a5d4051bfe06f3fcadb3004969b993f2535f6ce28116b"

RUN curl -L https://github.com/vrana/adminer/releases/download/v${ADMINER_VERSION}/adminer-${ADMINER_VERSION}${ADMINER_FLAVOUR}.php -o adminer.php && \
    echo "${ADMINER_CHECKSUM}  adminer.php"|sha256sum -c -

RUN curl -L https://raw.githubusercontent.com/vrana/adminer/master/designs/jukin/adminer.css -o adminer.css

COPY index.php /var/adminer/index.php
COPY php.ini /etc/php8/conf.d/99_docker.ini

EXPOSE 8080

USER adminer
CMD [ "php", "-S", "[::]:8080", "-t", "/var/adminer" ]

