# Fixing the Login Redirect Issue (j_spring_security_check)

When using HTTPS, some users may encounter an issue where the login page redirects from HTTPS to HTTP, resulting in login failures. This problem is associated with the j_spring_security_check handler in GeoServer.

Solution:
To preserve the HTTPS protocol, ensure the following line is added to your Nginx configuration:

nginx

```
proxy_set_header X-Forwarded-Proto https;
```

**Update web.xml:**

Location: docker-geoserver/build_data/hazelcast_cluster/web.xml or inside the WEB_INF folder.
Add the PROXY_BASE_URL for HTTPS access:

```
<context-param>
    <param-name>PROXY_BASE_URL</param-name>
    <param-value>https://<your-site-name>/geoserver</param-value>
</context-param>
```

Refer to the detailed [configuration](https://github.com/manikandanmohan10/docker-geoserver/blob/dev/strategy_docs/production/test.conf) in the setup guide.

Reason:
This configuration ensures that GeoServer recognizes it is running behind a proxy and avoids redirecting back to HTTP.

## Reference Solution:

**Faced issue on redirect https to https while login**

[Reddit discussion on login redirect issue](https://www.reddit.com/r/javahelp/comments/1fz25xh/geoserver_j_spring_security_check_on_login_keep/)
[docker container](https://hub.docker.com/r/kartoza/geoserver)
[csrf](https://docs.geoserver.org/stable/en/user/security/webadmin/csrf.html)
[proxy](https://stackoverflow.com/questions/68783126/issue-with-geoserver-login-with-ssl)


# 2 Issue

The server cannot or will not process the request due to something perceived as a client error, such as malformed request syntax, invalid request message framing, or deceptive request routing.

## Environment Variables to Update

Ensure the following environment variables are set correctly:

```
CSRF_WHITELIST:           geoserver.openskope.org (or your PROXY_BASE_URL)
SSL:                      TRUE (Enables HTTPS access)
PROXY_BASE_URL:          https://<your-site-name>/geoserver
FORCE_SSL:               TRUE (Force HTTPS)
Note: The CSRF_WHITELIST is essential for transmitting requests correctly.
```

**Nginx Configuration**

Ensure that the following line appears first in your Nginx configuration:

nginx

```
proxy_pass http://127.0.0.1:8000;
```

Common Issue: Page Not Redirecting

If you encounter issues with the page not redirecting correctly, refer to these resources for troubleshooting:

**CSRF**
the CSRF_WHITELIST is crucial for safeguarding web applications against CSRF attacks by restricting which domains can initiate requests that modify server state.

[nginx reverse proxy](https://github.com/kartoza/docker-geoserver/issues/222)
[Deployment documentaion](https://github-wiki-see.page/m/openskope/skope-api/wiki/GeoServer-Deployment-Documentation)
