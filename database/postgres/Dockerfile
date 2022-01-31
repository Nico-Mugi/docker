FROM postgres:11.6-alpine
#get postgres on a 11.6 alpine os

ENV POSTGRES_DB=db \
    POSTGRES_USER=usr
#set db name to db and user to usr

COPY scripts/CreateScheme.sql /docker-entrypoint-initdb.d
COPY scripts/InsertData.sql /docker-entrypoint-initdb.d
#copy local sql scripts to postgres image in /docker-entrypoint-initdb.d folder