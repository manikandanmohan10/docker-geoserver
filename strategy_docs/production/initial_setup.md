# GeoServer Deployment with Docker, Nginx, and Certbot

This guide outlines the deployment of the Kartoza GeoServer Docker container using Nginx for reverse proxy on a bare-metal server. It also covers handling login redirects over HTTPS.

## Prerequisites

Docker Engine installed.
Git installed.
Nginx installed for reverse proxy.

## Setup Instructions

### 1. Clone the Repository

```
git clone --branch production --single-branch https://github.com/manikandanmohan10/docker-geoserver
```

### 2. Update Environment Variables

Update the following environment variables (.env file or Docker Compose):

```
CSRF_WHITELIST:           geoserver.openskope.org (or your PROXY_BASE_URL)
GEOSERVER_DATA_DIR:        Path to GeoServer data
GEOSERVER_ADMIN_PASSWORD:  /run/secrets/<admin-password-file>
GEOSERVER_ADMIN_USER:      <admin-username>
COMMUNITY_EXTENSIONS:      Plugins from https://github.com/kartoza/docker-geoserver/blob/master/build_data/community_plugins.txt
SSL:                      TRUE (Enables HTTPS access)
PROXY_BASE_URL:            https://<your-site-name>/geoserver
FORCE_SSL:                 TRUE (Force HTTPS)
GEOSERVER_PORT:            <Port Number>
POSTGRES_USER:             <Postgres Username>
POSTGRES_PASS:             <Postgres Password>
POSTGRES_DB:               <Database Name>
```


### 3. Update web.xml

Location: docker-geoserver/build_data/hazelcast_cluster/web.xml or inside the WEB_INF folder.

Add the PROXY_BASE_URL for HTTPS access:

```
<context-param>
    <param-name>PROXY_BASE_URL</param-name>
    <param-value>https://<your-site-name>/geoserver</param-value>
</context-param>
```


### 4. GeoServer Docker Setup

Use the provided [docker-compose.yml](https://github.com/manikandanmohan10/docker-geoserver/blob/develop/docker-compose.yml) for setup.

### 5. Nginx Configuration

Create an Nginx configuration file under:

```
cd /etc/nginx/conf.d
```

Refer to the detailed [configuration](https://github.com/manikandanmohan10/docker-geoserver/blob/dev/strategy_docs/production/test.conf) in the setup guide.

### 6. Commands
Run Docker Compose

```
docker compose -f docker-compose.yml up -d
```

### 7.Certbot Setup

Prepare Certbot by creating a symbolic link:

```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Run Certbot to obtain and configure SSL certificates:

```
sudo certbot --nginx
```

This command sets up SSL for your Nginx configuration in one step.

