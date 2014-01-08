Bash CGI Example
================

Development server
------------------
Quickly get up an running using
[Python's CGIHTTPServer](http://docs.python.org/2/library/cgihttpserver.html):

    python -m CGIHTTPServer

Caveats:
  - scripts need to be placed in a sub-folder named `cgi-bin/` or `htbin/`
  - every response gets the status **200 Script output follows**

Nginx
-----
As [Nginx](http://nginx.org/) only supports FastCGI out of the box,
we also need to install [fcgiwrap](https://github.com/gnosek/fcgiwrap).

On Ubuntu: `apt-get install nginx fcgiwrap`
On Arch: `pacman -S nginx fcgiwrap`


Example nginx config (`/etc/nginx/sites-enabled/default`):

```
server {
    listen   80;
    server_name  localhost;
    access_log  /var/log/nginx/access.log;

    location / {
        root /srv/static;
        autoindex on;
        index index.html index.htm;
    }

    location ~ ^/cgi {
        root /srv/my_cgi_app;
        rewrite ^/cgi/(.*) /$1 break;

        include fastcgi_params;
        fastcgi_pass unix:/var/run/fcgiwrap.socket;
        fastcgi_param SCRIPT_FILENAME /srv/my_cgi_app$fastcgi_script_name;
    }
}
```

Change the **root** and **fastcgi_param** lines to a directory containing CGI
scripts, e.g. the `cgi-bin/` dir in this repository.

If you are a control freak and run `fcgiwrap` manually,
be sure to change **fastcgi_pass** accordingly. The path listed in the example
is the default in Ubuntu when using the out-of-the-box fcgiwrap setup.
