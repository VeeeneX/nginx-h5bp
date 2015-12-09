# Nginx + H5BP config

[![Docker Stars](https://img.shields.io/docker/stars/dockette/nginx-h5bp.svg?style=flat)](https://hub.docker.com/r/dockette/nginx-h5bp/)
[![Docker Pulls](https://img.shields.io/docker/pulls/dockette/nginx-h5bp.svg?style=flat)](https://hub.docker.com/r/dockette/nginx-h5bp/)

Nginx with SSL / HSTS and HTTP2 module + H5BP configs. 

Thanks to [h5bp/server-configs-nginx](https://github.com/h5bp/server-configs-nginx) community.

## Based

This image is based on [Dockette/Nginx](https://github.com/dockette/nginx-h5bp) with full support of SSL / HSTS and HTTP2.

## Default

Without any volumes Nginx listen IPv4 and IPv6 on both ports 80/443 and serve 444 No content.

## Usage

See documentation on [h5bp/server-configs-nginx](https://github.com/h5bp/server-configs-nginx/blob/master/doc/TOC.md)

```sh
docker run \
    -p 80:80 \ 
    -p 443:433 \ 
    -v /home/nginx/sites:/etc/nginx/sites-enabled \ 
    -v /home/nginx/dhparam2048.pem:/etc/nginx/dhparam2048.pem \ 
    --name nginx \ 
    dockette/nginx-h5bp:latest \ 
```

Your site example could looks like that

```nginx
# www to non-www redirect -- duplicate content is BAD:
# https://github.com/h5bp/html5-boilerplate/blob/5370479476dceae7cc3ea105946536d6bc0ee468/.htaccess#L362
# Choose between www and non-www, listen on the *wrong* one and redirect to
# the right one -- http://wiki.nginx.org/Pitfalls#Server_Name
server {
  # don't forget to tell on which port this server listens
  listen [::]:80;
  listen 80;

  # listen on the www host
  server_name www.example.com;

  # and redirect to the non-www host (declared below)
  return 301 $scheme://example.com$request_uri;
}

server {
  # listen [::]:80 accept_filter=httpready; # for FreeBSD
  # listen 80 accept_filter=httpready; # for FreeBSD
  # listen [::]:80 deferred; # for Linux
  # listen 80 deferred; # for Linux
  listen [::]:80;
  listen 80;

  # The host name to respond to
  server_name example.com;

  # Path for static files
  root /sites/example.com/public;

  # Specify a charset
  charset utf-8;

  # Custom 404 page
  error_page 404 /404.html;

  # Include the basic h5bp config set
  include h5bp/basic.conf;
}
```