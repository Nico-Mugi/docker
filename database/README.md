# 0- Setup the Dockerfile :
```
FROM postgres:11.6-alpine
#get postgres on a 11.6 alpine os

ENV POSTGRES_DB=db \
    POSTGRES_USER=usr
#set db name to db and user to usr

COPY scripts/CreateScheme.sql /docker-entrypoint-initdb.d
COPY scripts/InsertData.sql /docker-entrypoint-initdb.d
#copy local sql scripts to postgres image in /docker-entrypoint-initdb.d folder
```
# 1- build the image :
``docker build -t nicolastvn/database ./postgres``  
# 1.b rebuild the image :
``docker build --no-cache -t nicolastvn/database ./postgres``
# 2- Build adminer : 
``docker build --no-cache -t nicolastvn/adminer ./adminer``
# 3- Create network : 
``docker network create app-network`` ***only once***
# 4- Run the image : 
``docker run -d -e POSTGRES_PASSWORD=pwd -v /my/own/datadir:/var/lib/postgresql/data --network=app-network --name database nicolastvn/database``
-e = env var
-v = volume manipulation (persist data)
--network = network to use
# 5- Run adminer : 
``docker run -d --network=app-network -p 8080:8080 --name adminer nicolastvn/adminer``
-p = portDestination:portForwarded
# 5- Go to localhost:8080
# 6- login : 
Système : PostgreSQL   
Serveur : database   
Utilisateur : usr   
Mot de passe : pwd   
Base de données : db   