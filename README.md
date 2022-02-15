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

## Docker Compose 
docker-compose.yml to configure docker compose launch parameters :
```yml
version: '3.3'
services:
  app1:
    build:
      ./src_app1
    networks:
      - networkName
    depends_on:
      - dependencyContainerName
  app2:
    build:
      ./src_app2
    ports:
      - 82:80
networks:
  app-network:
```
Launch docker compose : ``docker-compose up``   

## GitHub Actions
Setting up a file .github/workflows/.main.yml :
```yml
name: CI devops 2022 CPE
on:
#to begin you want to launch this job in main and develop
  push:
    branches: 
      - main
      - develop
  pull_request:

jobs:
  test-backend:
    runs-on: ubuntu-18.04
    steps:
      #checkout your github code using actions/checkout@v2.3.3
      - uses: actions/checkout@v2.3.3

      #do the same with another action (actions/setup-java@v2) that enable to setup jdk 11
      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          distribution: 'zulu'
          java-version: '11'
            
      #finally build your app with the latest command
      - name: Build and test with Maven
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}  # Needed to get PR information, if any
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }} 
        run: mvn -B verify sonar:sonar -Dsonar.projectKey=Nico-Mugi_docker -Dsonar.organization=nico-mugi
            -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }} --file ./backend/simple-api
            
  # define job to build and publish docker image
  build-and-push-docker-image:
    needs: test-backend
    # run only when code is compiling and tests are passing
    runs-on: ubuntu-latest
    # steps to perform in job
    steps:
      - name: Login to DockerHub
        run: docker login -u ${{ secrets.DOCKERHUB_USERNAME }} -p ${{ secrets.DOCKERHUB_TOKEN }}
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Build image and push backend
        uses: docker/build-push-action@v2
        with:
        # relative path to the place where source code with Dockerfile is located
          context: ./backend
        # Note: tags has to be all lower-case
          tags: ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-cpe-backend
          # build on feature branches, push only on main branch
          push: ${{ github.ref == 'refs/heads/main' }}
      - name: Build image and push database
        uses: docker/build-push-action@v2
        with:
        # relative path to the place where source code with Dockerfile is located
          context: ./database/postgres
        # Note: tags has to be all lower-case
          tags: ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-cpe-database
          # build on feature branches, push only on main branch
          push: ${{ github.ref == 'refs/heads/main' }}

      - name: Build image and push httpd
        uses: docker/build-push-action@v2
        with:
        # relative path to the place where source code with Dockerfile is located
          context: ./httpd
        # Note: tags has to be all lower-case
          tags: ${{secrets.DOCKERHUB_USERNAME}}/tp-devops-cpe-httpd
          # build on feature branches, push only on main branch
          push: ${{ github.ref == 'refs/heads/main' }}
```