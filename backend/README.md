# V1 avec uniquement le Main 
```
FROM openjdk:11   
#import openjdk
COPY Main.java . 
#copy main.java to the image  
RUN javac Main.java   
#compile main.java
CMD ["java", "Main"]   
#launch main using java command
```
# Build l'image
``docker build --no-cache -t nicolastvn/backend .``
# set the network as the same than database
``docker run -d --network=app-network -p 8081:8080 --name backend nicolastvn/backend``

# V2 avec simple-api
```
#Use maven image and call it myapp-build
FROM maven:3.6.3-jdk-11 AS myapp-build

#Define /opt/myapp as an ENV variable : MYAPP_HOME
ENV MYAPP_HOME /opt/myapp

#Setting the working directory /opt/myapp
WORKDIR $MYAPP_HOME

#copy pom.xml and src folder from simple-api in local to docker /opt/myapp
COPY simple-api/pom.xml .
COPY simple-api/src ./src


#build project
RUN mvn dependency:go-offline
RUN mvn package -DskipTests

#Use openjdk 11 jre
FROM openjdk:11-jre

#Define /opt/myapp as an ENV variable : MYAPP_HOME
ENV MYAPP_HOME /opt/myapp

#Setting the working directory /opt/myapp
WORKDIR $MYAPP_HOME

#copy jar in target and name it myapp.jar
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

#run api at startup
ENTRYPOINT java -jar myapp.jar
```