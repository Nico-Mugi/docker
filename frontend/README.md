docker run -p 81:80 -d --network=app-network --name frontend nicolastvn/frontend
docker cp frontend:/usr/local/apache2/conf/httpd.conf .   


ServerName localhost
<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://backend:808/
    ProxyPassReverse / http://backend:8080/
</VirtualHost>

ajouter ça au httpd.conf 
Rajouter le copy dans le dockerfile

décommenté la ligne
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so