FROM centos:7


RUN yum update -y

ENV NGINX_VERSION 1.9.11
ENV NGINX_DEVEL_KIT_VERSION 0.2.19
ENV LUA_NGINX_MODULE_VERSION 0.10.1rc1
ENV LUAROCKS_VERSION 2.0.8

RUN yum install -y epel-release
RUN yum install -y luajit-devel gcc make pcre-devel zlib-devel openssl-devel lua-devel unzip


WORKDIR /usr/local/src/
RUN curl -L -O http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz
RUN curl -L -o ngx_devel_kit.tar.gz https://github.com/simpl/ngx_devel_kit/archive/v${NGINX_DEVEL_KIT_VERSION}.tar.gz
RUN curl -L -o lua-nginx-module.tar.gz https://github.com/openresty/lua-nginx-module/archive/v${LUA_NGINX_MODULE_VERSION}.tar.gz
RUN curl -L -O http://luarocks.org/releases/luarocks-${LUAROCKS_VERSION}.tar.gz

RUN tar zxf /usr/local/src/ngx_devel_kit.tar.gz
RUN tar zxf /usr/local/src/lua-nginx-module.tar.gz
RUN tar zxf /usr/local/src/nginx-${NGINX_VERSION}.tar.gz 
RUN tar zxf /usr/local/src/luarocks-${LUAROCKS_VERSION}.tar.gz

WORKDIR /usr/local/src/nginx-${NGINX_VERSION}
RUN ./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=root --group=root --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_random_index_module --with-http_secure_link_module --with-http_stub_status_module --with-http_auth_request_module --with-file-aio --with-ipv6 --with-http_v2_module --add-module=/usr/local/src/lua-nginx-module-${LUA_NGINX_MODULE_VERSION} --add-module=/usr/local/src/ngx_devel_kit-${NGINX_DEVEL_KIT_VERSION} --with-cc-opt='-O2 -g -pipe -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector --param=ssp-buffer-size=4 -m64 -mtune=generic'
RUN make -j2
RUN make install

RUN mkdir -p /usr/share/nginx/html /etc/nginx/conf.d /var/cache/nginx
ADD files/etc/sysconfig/nginx /etc/sysconfig/nginx
ADD files/etc/nginx/nginx.conf /etc/nginx/nginx.conf

WORKDIR /usr/local/src/luarocks-${LUAROCKS_VERSION}
RUN ./configure
RUN make
RUN make install

RUN /bin/bash -c 'luarocks path > /etc/profile.d/lua.sh'

WORKDIR /root/
RUN rm -rf /usr/local/src/*

RUN ln -sf /dev/stdout /var/log/nginx/access.log
RUN ln -sf /dev/stderr /var/log/nginx/error.log

RUN /usr/local/bin/luarocks install md5


EXPOSE 80 443

VOLUME [ "/etc/nginx/conf.d", "/usr/share/nginx/html" ]
CMD /usr/sbin/nginx -g "daemon off;"

