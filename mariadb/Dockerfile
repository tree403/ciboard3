FROM alpine:3.16

# https://git.alpinelinux.org/aports/tree/main/mariadb/mariadb.pre-install
RUN set -eux; \
	addgroup -S mysql; \
	adduser -S -D -h /var/lib/mysql -s /bin/sh -G mysql -g mysql mysql;

ENV LANG en_US.utf8

RUN mkdir /docker-entrypoint-initdb.d

# add gosu for easy step-down from root
# https://github.com/tianon/gosu/releases
# gosu key is B42F6819007F00F88E364FD4036A9C25BF357DD4
ENV GOSU_VERSION 1.14

ARG GPG_KEYS=177F4010FE56CA3336300305F1656F24C74CD1D8
# pub   rsa4096 2016-03-30 [SC]
#         177F 4010 FE56 CA33 3630  0305 F165 6F24 C74C D1D8
# uid           [ unknown] MariaDB Signing Key <signing-key@mariadb.org>
# sub   rsa4096 2016-03-30 [E]
# install "libjemalloc2" as it offers better performance in some cases. Use with LD_PRELOAD
# install "pwgen" for randomizing passwords
# install "tzdata" for /usr/share/zoneinfo/
# install "xz-utils" for .sql.xz docker-entrypoint-initdb.d files
# install "zstd" for .sql.zst docker-entrypoint-initdb.d files
# hadolint ignore=SC2086

RUN set -eux; \
		\
		apk add --no-cache --virtual .gosu-deps \
			ca-certificates \
			dpkg \
			gnupg \
		; \
		\
		dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')"; \
		wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch"; \
		wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc"; \
		\
# verify the signature
		export GNUPGHOME="$(mktemp -d)"; \
		gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4; \
		gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
		command -v gpgconf && gpgconf --kill all || :; \
		rm -rf "$GNUPGHOME" /usr/local/bin/gosu.asc; \
		\
# clean up fetch dependencies
		apk del --no-network .gosu-deps; \
		\
		chmod +x /usr/local/bin/gosu; \
		gosu --version; \
		gosu nobody true

RUN set -eux; \
		\
		apk add --no-cache --virtual .mariadb-rundeps \
			bash \
			pwgen \
			tzdata \
			zstd-dev \
			mariadb \
			mariadb-client \
			mariadb-server-utils \
		; \
		\
		rm -f /var/cache/apk/*;

RUN set -eux; \
		\
# purge and re-create /var/lib/mysql with appropriate ownership
		rm -rf /var/lib/mysql; \
		mkdir -p /var/lib/mysql /var/run/mysqld; \
		chown -R mysql:mysql /var/lib/mysql /var/run/mysqld; \
# ensure that /var/run/mysqld (used for socket and lock files) is writable regardless of the UID our mysqld instance ends up having at runtime
		chmod 777 /var/run/mysqld; \
# comment out a few problematic configuration values
		find /etc/my.cnf.d/ -name '*.cnf' -print0 \
			| xargs -0 grep -lZE '^(bind-address|log|user\s)' \
			| xargs -rt -0 sed -Ei 's/^(bind-address|log|user\s)/#&/'; \
# don't reverse lookup hostnames, they are usually another container
		printf "[mariadb]\nhost-cache-size=0\nskip-name-resolve\n" > /etc/my.cnf.d/05-skipcache.cnf; \
# enables networking
		sed -i "1,/skip-networking/s//#skip-networking/" /etc/my.cnf.d/mariadb-server.cnf; \
		sed -i "1,/#bind-address=0.0.0.0/s//bind-address=0.0.0.0/" /etc/my.cnf.d/mariadb-server.cnf;

ENV DATADIR /var/lib/mysql

# this 777 will be replaced by 700 at runtime (allows semi-arbitrary "--user" values)
RUN mkdir -p "$DATADIR" && chown -R mysql:mysql "$DATADIR" && chmod 777 "$DATADIR"

VOLUME /var/lib/mysql

COPY healthcheck.sh /usr/local/bin/
COPY docker-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 3306
CMD ["mariadbd"]
