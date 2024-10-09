# GeoServer Deployment with Docker, Nginx, and Cerbot

This document outlines the process of deploying the Kartoza GeoServer Docker container with Nginx on a bare metal server. It also addresses common issues encountered with login redirects over HTTPS and provides solutions for `j_spring_security_check` and CSRF issues.

## Prerequisites

- Docker & Docker Compose installed on your server.
- Nginx installed locally on the server for reverse proxy.
- Certbot.

## Step-by-Step Setup

### 1. GeoServer Docker Setup

[click here](https://github.com/manikandanmohan10/docker-geoserver/blob/develop/docker-compose.yml)

### 2. Nginx Configuration

### 4. Fixing the Login Redirect Issue (j_spring_security_check)
When using HTTPS, some users may face an issue where the login page redirects from HTTPS to HTTP, causing login failures. This issue is related to the j_spring_security_check handler in GeoServer.

Solution: Ensure the following is set in your Nginx configuration to preserve the protocol:

nginx
```
proxy_set_header X-Forwarded-Proto https;
```

This ensures that GeoServer knows it is running behind a proxy and doesn't redirect back to HTTP.

Reference Solution:
Faced issue on redirect https to https while login
[Reddit discussion on login redirect issue](https://www.reddit.com/r/javahelp/comments/1fz25xh/geoserver_j_spring_security_check_on_login_keep/)


[docker container](https://hub.docker.com/r/kartoza/geoserver)
[csrf](https://docs.geoserver.org/stable/en/user/security/webadmin/csrf.html)
[proxy](https://stackoverflow.com/questions/68783126/issue-with-geoserver-login-with-ssl)

