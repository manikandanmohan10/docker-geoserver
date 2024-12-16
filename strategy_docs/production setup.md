# GeoServer Deployment with Docker, Nginx, and Cerbot

This document outlines the process of deploying the Kartoza GeoServer Docker container with Nginx on a bare metal server.
It also addresses common issues encountered with login redirects over HTTPS and provides solutions for `j_spring_security_check` and CSRF issues.

## Prerequisites

- [Docker](https://docs.docker.com/engine/install/ubuntu/) & Docker Compose installed on your server.
- [Nginx](https://docs.vultr.com/how-to-install-nginx-web-server-on-ubuntu-24-04?ref=9141994&utm_source=performance-max-apac&utm_medium=paidmedia&obility_id=16876059738&&utm_campaign=APAC_-_India_-_Performance_Max_-_1001&utm_term=&utm_content=&ref=9141994&gad_source=1&gclid=Cj0KCQiA0--6BhCBARIsADYqyL8qSDD8yKeGT1Nxk3vMHSp7t2meQCy6NYQyOlZwluYTaDS0EzVH-DEaAse1EALw_wcB) installed locally on the server for reverse proxy.
- [Git]
- [Certbot](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal).

## Step-by-Step Setup


### Step 1: Clone the repo
clone your geoserver docker, please fork the `https://github.com/kartoza/docker-geoserver`
and then clone your forked repo
Ex:

```
git clone https://github.com/manikandanmohan10/docker-geoserver.git
git checkout dev
```


### Step 2: change docker-compose.yml

Change `docker-compose.yml` file according to the below file, It is required for production setup. If you are in `dev` branch this correction not required leave it.

[click here](https://github.com/manikandanmohan10/docker-geoserver/blob/develop/docker-compose.yml)


### Step 3: Change the .env file:


```
GEOSERVER_ADMIN_USER=admin
GEOSERVER_ADMIN_PASSWORD=strategy@123

SSL=true
#HTTP_PORT=8080
#HTTP_PROXY_NAME=geoserver.portail-repae.com
#HTTP_PROXY_PORT=8000
#HTTP_REDIRECT_PORT=
CSRF_WHITELIST=geoserver.portail-repae.com
HTTP_CONNECTION_TIMEOUT=20000
HTTPS_PORT=8443
HTTPS_MAX_THREADS=150
HTTPS_CLIENT_AUTH=
HTTPS_PROXY_NAME=https://geoserver.portail-repae.com
HTTPS_PROXY_BASE_URL=https://geoserver.portail-repae.com/geoserver
HTTPS_PROXY_PORT=443
GEOSERVER_PORT=8000
POSTGRES_USER=postgres
POSTGRES_PASS=strategy@123

```

### Step 3. Run  the docker cmd

```
docker compose up -d
```

### Setup 4: Test the project is running:

```
curl http://localhost:8000/geoserver/
```

### Setup 5:Nginx Configuration

**Set Up NGINX:**

Install NGINX locally on your machine or server.
Place the nginx.conf in the** /etc/nginx/conf.d/** directory or as specified in your local configuration.
Restart NGINX to apply changes: `sudo systemctl restart nginx`.

```
sudo nano  /etc/nginx/conf.d/geoserver.conf
```


```

server {
    listen 443 ssl;
    server_name geoserver.portail-repae.com;
    ssl_certificate /etc/letsencrypt/live/geoserver.portail-repae.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/geoserver.portail-repae.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        client_max_body_size 250M;
    }

    location /geoserver {
        proxy_redirect http://127.0.0.1:8000/geoserver https://$host/geoserver;

        proxy_pass http://127.0.0.1:8000/geoserver;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Script-Name /geoserver;
        client_max_body_size 250M;
}

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;


}

server {
    if ($host = geoserver.portail-repae.com) {
        return 301 https://$host$request_uri;
    }


    listen 80;
    server_name geoserver.portail-repae.com;
    return 301 https://$host$request_uri;


}
```

**Start NGINX**

```
sudo systemctl start nginx
```

**Enable NGINX to Start on Boot**

```
sudo systemctl enable nginx
```

**See status**

```
sudo systemctl status nginx
```

**Restart**
```
sudo systemctl restart nginx
```

**Reload nginx**

```
sudo nginx -t && sudo systemctl reload nginx
```

#### docker compose down.

```
docker compose down
```

