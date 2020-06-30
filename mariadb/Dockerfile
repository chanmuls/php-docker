FROM mariadb:10.5.4

LABEL maintainer="devtopia@naver.com"

RUN rm /etc/mysql/conf.d/docker.cnf
ADD ./conf.d/docker.cnf /etc/mysql/conf.d/docker.cnf

EXPOSE 3306

CMD ["mysqld"]