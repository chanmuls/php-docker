FROM nginx

ENV TZ=Asia/Seoul
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
ENV DEBIAN_FRONTEND noninteractive

RUN rm -rf /etc/apt/sources.list
COPY ./sources.list /etc/apt/sources.list

RUN echo $TZ > /etc/timezone && \
    apt-get -y upgrade && apt-get update && apt-get install -y tzdata && \
    rm /etc/localtime && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

RUN apt-get install -y software-properties-common locales locales-all \
    iputils-ping apt-transport-https lsb-release ca-certificates \
    iproute2 \
    curl \
    wget \
    vim \
    netcat \
    gcc

RUN wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
RUN echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | tee /etc/apt/sources.list.d/php.list

RUN apt-get -y upgrade && apt-get update && LC_ALL=en_US.UTF-8 add-apt-repository -y ppa:ondrej/php \
    && mkdir /run/php

RUN apt-get -y install php7.3 php7.3-fpm php7.3-mbstring \
    php7.3-mysql php7.3-mysqli php7.3-gd curl php7.3-curl php-redis php7.3-mbstring sendmail supervisor \
    php-pear apt-transport-https composer unzip php7.3-redis php7.3-igbinary \
    php-igbinary php-msgpack

RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# http://download.redis.io/releases/
RUN wget http://download.redis.io/releases/redis-5.0.9.tar.gz
RUN tar xzf redis-5.0.9.tar.gz
RUN mv redis-5.0.9 /etc/redis
RUN rm -rf redis-5.0.9.tar.gz

WORKDIR /etc/redis

RUN make

RUN mkdir -p /etc/nginx/logs
RUN mkdir -p /etc/nginx/sites-available
RUN mkdir -p /etc/nginx/sites-enabled
RUN mkdir -p /etc/nginx/conf.d

ADD ./nginx/nginx.conf /etc/nginx/
ADD ./nginx/public_params /etc/nginx/
ADD ./nginx/php-fpm /etc/nginx/conf.d/

RUN mkdir -p /var/www
RUN chown :staff /var/www
RUN chmod 775 /var/www

COPY ./docker-entrypoint.sh /entry-point.sh

RUN chmod +x /entry-point.sh
RUN sh /entry-point.sh

COPY ./fpm/php.ini /etc/php/7.3/fpm/php.ini
COPY ./fpm/php-fpm.conf /etc/php/7.3/fpm/php-fpm.conf
COPY ./fpm/www.conf /etc/php/7.3/fpm/pool.d/www.conf

COPY ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf

RUN update-alternatives --set php /usr/bin/php7.3

RUN /etc/init.d/php7.3-fpm restart

WORKDIR /var/www

CMD ["/usr/bin/supervisord"]
