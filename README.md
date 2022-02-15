# Introduction
## Repo for practing DevOps : Docker, GitHub Actions, Sonar Cloud, Ansible    
For generic documentation, it is in this current README    
This repo is organized so each subpart have its own README   
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
Build an image and make it a container : ``docker build -t nicolastvn/name ./src``     
Rebuild an image : ``docker build --no-cache -t nicolastvn/name ./src``   
## Use a container
Run : ``docker run -d -p 81:80 -e ENV_VAR=arg -v /my/own/datadir:/var/lib/postgresql/data --network=networkName --name name nicolastvn/name``   
-d = detached terminal *do not specify if you need logs*   
-e = define env var *useful for password so they don't end up in your git*   
-v = volume manipulation *persist data*   
-p = specify port *portDestination:portForwarded*   
--network = network to use *need to be created first (see Interactions between containers)*   

Run with a terminal : ``docker run -it name command``   
View current containers running : ``docker ps``   
Force stopping and removing a container : ``docker rm -f name``   
Execute a command into a running container : ``docker exec name command args...``    
See running ports on specific container : ``docker port name``       

## Interactions between containers 
Create a network  : ``docker network create networkName`` *only once*


launch avec docker-compose up