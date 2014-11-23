FROM ubuntu:14.04

MAINTAINER Kyle Drake, kyledrake@neocities.org

RUN apt-get update

RUN apt-get upgrade -y
RUN apt-get install -y ntp fail2ban python-software-properties software-properties-common build-essential zlib1g-dev libpcre3 libpcre3-dev libssl-dev openssl curl

RUN add-apt-repository -y ppa:brightbox/ruby-ng
RUN apt-get update
RUN apt-get install -y ruby2.1

WORKDIR /root

RUN curl -L -o ngx_cache_purge.tar.gz https://github.com/neocities/ngx_cache_purge/archive/2.1.tar.gz
RUN tar -zxf ngx_cache_purge.tar.gz
RUN mv ngx_cache_purge-2.1 ngx_cache_purge

RUN curl -o nginx.tar.gz http://nginx.org/download/nginx-1.6.2.tar.gz
RUN tar -zxf nginx.tar.gz
RUN mv nginx-1.6.2 nginx

WORKDIR /root/nginx

RUN ./configure --prefix=/usr --conf-path=/etc/nginx/nginx.conf --sbin-path=/usr/sbin/nginx --pid-path=/var/run/nginx.pid --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-http_ssl_module --add-module=../ngx_cache_purge --with-http_realip_module
RUN make
RUN make install

RUN mkdir /etc/nginx/certs
RUN chmod go-rw /etc/nginx/certs

COPY nginx/nginx.conf /etc/nginx/nginx.conf
COPY nginx/error_pages /etc/nginx/error_pages
COPY secrets/neocities.org.crt /etc/nginx/certs/
COPY secrets/neocities.org.key /etc/nginx/certs/
COPY secrets/neocities-upstream.conf /etc/nginx/neocities-upstream.conf
COPY secrets/proxyauth.txt /root/
COPY secrets/proxy.key /root/

WORKDIR /etc/nginx

RUN curl --user `cat /root/proxyauth.txt` -o sslsites.tar.gz https://neocities.org/sysops/proxy/sslsites.tar.gz
RUN tar -zxf sslsites.tar.gz
RUN curl --user `cat /root/proxyauth.txt` -o map.txt https://neocities.org/sysops/proxy/map.txt

WORKDIR /root

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]