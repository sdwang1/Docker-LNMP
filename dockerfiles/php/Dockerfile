FROM php:7.2-fpm

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 更新安装依赖包和PHP核心扩展
RUN apt-get update && apt-get install -y \
        libxml2-dev libbz2-dev libssl-dev libcurl4-openssl-dev \
        libjpeg-dev libpng-dev libfreetype6-dev libgmp-dev \
        libreadline6-dev libxslt1-dev libzip-dev \
        --no-install-recommends && rm -rf /var/lib/apt/lists/* \
        && docker-php-ext-install -j$(nproc) \
            iconv gettext curl mysqli pdo pdo_mysql \
            zip mbstring bcmath opcache xml simplexml \
            bz2 sockets shmop \
        && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
        && docker-php-ext-install -j$(nproc) gd

# 安装 PECL 扩展，这里我们安装Redis
RUN pecl install -o -f redis  \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis

# 安装第三方扩展，这里是Xcache扩展
#RUN curl -fsSL 'https://xcache.lighttpd.net/pub/Releases/3.2.0/xcache-3.2.0.tar.gz' -o xcache.tar.gz \
#    && mkdir -p xcache \
#    && tar -xf xcache.tar.gz -C xcache --strip-components=1 \
#    && rm xcache.tar.gz \
#    && ( \
#        cd xcache \
#        && phpize \
#        && ./configure --enable-xcache \
#        && make -j$(nproc) \
#        && make install \
#    ) \
#    && rm -r xcache \
#    && docker-php-ext-enable xcache

# 安装 Composer
# Note 官方提供了Composer镜像，拉取composer镜像执行也可以达到目的，因为我们使用composer只是为了管理依赖包，这里项目中还安装了一些
# PHP扩展，composer执行时会检查环境是否安装了依赖，如果直接用composer镜像就需要把这些扩展安装到镜像里，比较麻烦
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

WORKDIR /data

# Write Permission
RUN usermod -u 1000 www-data

