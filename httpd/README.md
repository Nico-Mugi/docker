# Run l'image : 
``docker run -p 81:80 -d --network=app-network --name frontend nicolastvn/frontend``
# Get the httpd.conf from first initialization so we can edit it locally
``docker cp frontend:/usr/local/apache2/conf/httpd.conf .   ``

# Add Proxy configuration into the copied httpd.conf
### Backend will forward his 8080 port to the /api url 
### Frontend will forward his 80 port to / url
```
ServerName localhost
<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass /api http://backend:8080/
    ProxyPassReverse /api http://backend:8080/
    ProxyPass / http://front:80/
    ProxyPassReverse / http://front:80/
</VirtualHost>
```

# Uncomment used packages :
```
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so
```

# Add copy to dockerfile to change httpd.conf on launch
``COPY ./httpd.conf /usr/local/apache2/conf/httpd.conf``

# Copy index.html to htdocs to test proxy is reachable
``COPY ./index.html /usr/local/apache2/htdocs``
