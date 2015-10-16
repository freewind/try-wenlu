Use vagrant
-----------

```
mkdir ubuntu14.04
cd ubuntu14.04
vagrant box add ubuntu14.04 https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box
vagrant init ubuntu14.04
```

Add to `Vagrantfile`

```
config.vm.network "forwarded_port", guest: 80, host: 10080
```

Then

```
vagrant up
vagrant ssh
```

Inside ubuntu
--------------


```
mkdir download
cd download
wget http://nginx.org/download/nginx-1.7.8.tar.gz

sudo apt-get install -y git
git clone https://github.com/cuber/ngx_http_google_filter_module
git clone https://github.com/yaoweibin/ngx_http_substitutions_filter_module

tar -zxvf nginx-1.7.8.tar.gz
cd nginx-1.7.8

sudo apt-get install -y libpcre3-dev libssl-dev zlib1g-dev

./configure \
  --with-http_ssl_module \
  --add-module=../ngx_http_google_filter_module \
  --add-module=../ngx_http_substitutions_filter_module

make
sudo make install
```


Some usefull information about nginx will show:

```
  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
```

Config nginx
------------

```
sudo vim /usr/local/nginx/conf/nginx.conf
```

Find the `location /` part, modify it to:

```
    resolver 8.8.8.8;
    location / {
        google on;
        google_scholar on;
        # root   html;
        # index  index.html index.htm;
    }
```

Start nginx
-----------

```
sudo /usr/local/nginx/sbin/nginx
```

Try
---

Make sure the ubuntu machine can access google.

Then visit: `http://127.0.0.1:10080` (rather than `localhost`), you will see a live google. And if you've searched anything in it, say `aaa`, you will see it's logged in `/usr/local/nginx/logs/access.log`:

```
10.0.2.2 - - [16/Oct/2015:11:37:04 +0000] "GET /search?newwindow=1&site=&source=hp&q=aaa&oq=aaa&gs_l=hp.3...
```

So we know which keywords are used by users.

Note: if it's not working, check `/usr/local/nginx/logs/error.log` to see if there is any errors. If not, try to clear cookies or using an incognite window.

Other useful commands
---------------------

Stop the nginx:

```
sudo ./nginx -s stop
```

See error log:

```
less /usr/local/nginx/logs/error.log
```