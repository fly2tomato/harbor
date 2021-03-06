FROM photon:2.0

ENV PGDATA /var/lib/postgresql/data

## have both mysql and pgsql installed.
RUN tdnf distro-sync -y \
    && tdnf remove -y toybox \
    && tdnf install -y sed shadow procps-ng gawk gzip sudo net-tools glibc-i18n >> /dev/null\
    && groupadd -r -g 10000 mysql && useradd --no-log-init -r -g 10000 -u 10000 mysql \
    && tdnf install -y mariadb-server mariadb mariadb-devel python2 python2-devel python-pip gcc \
    linux-api-headers glibc-devel binutils zlib-devel openssl-devel postgresql >> /dev/null \
    && pip install mysqlclient alembic psycopg2 \
    && mkdir /docker-entrypoint-initdb.d /docker-entrypoint-updatedb.d \
    && rm -fr /var/lib/mysql \
    && mkdir -p /var/lib/mysql /var/run/mysqld \
    && chown -R mysql:mysql /var/lib/mysql /var/run/mysqld \
    && chmod 777 /var/run/mysqld /docker-entrypoint-initdb.d /docker-entrypoint-updatedb.d \
    && mkdir -p /harbor-migration \
    && touch /etc/localtime.bak \
    && groupadd -r postgres --gid=999 \
    && useradd -r -g postgres --uid=999 postgres \
    && mkdir -p /run/postgresql \
    && chown -R postgres:postgres /run/postgresql \
    && chmod 2777 /run/postgresql \
    && mkdir -p "$PGDATA" && chown -R postgres:postgres "$PGDATA" && chmod 777 "$PGDATA" \
    && sed -i "s|#listen_addresses = 'localhost'.*|listen_addresses = '*'|g" /usr/share/postgresql/postgresql.conf.sample \
    && sed -i "s|#unix_socket_directories = '/tmp'.*|unix_socket_directories = '/run/postgresql'|g" /usr/share/postgresql/postgresql.conf.sample \
    && touch /usr/share/locale/locale.alias \
    && locale-gen.sh en_US.UTF-8 \
    && tdnf clean all

COPY ./db/my.cnf /etc/

VOLUME /var/lib/postgresql/data

WORKDIR /harbor-migration

COPY ./ ./

ENTRYPOINT ["./docker-entrypoint.sh"] 
