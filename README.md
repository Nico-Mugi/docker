# Introduction
## Repo for practing DevOps : Docker, GitHub Actions, Sonar Cloud, Ansible    
For generic documentation, it is in this current README
This repo is organized so every subparts have their own README   
- [Database](database/README.md)
- [BackEnd](backend/README.md)
- [Proxy](httpd/README.md)
- [FrontEnd](front/README.md)
- [Ansible](ansible/README.md)    
# Documentation
## Docker
Search images in the store :
``docker search``   
View local images :
``docker images``   
View detail of a specific image :
``docker inspect imageName``   
Pull image from the store : 
``docker pull imageName[:version]``   
## Construct a DockerFile 
Specify base image : ``FROM imageName[:version]``   
Run a bash command : ``RUN bachCommand``   
Run a bash command on startup : ``CMD ["bashCommand", "arg1", args...]``   
Specify a port : ``EXPOSE portNumber``   
Add env var : ``ENV VAR_NAME arg``   
Copy files : ``COPY src dest``   
Build an image : ``docker build --name name nicolastvn/name``   

launch avec docker-compose up