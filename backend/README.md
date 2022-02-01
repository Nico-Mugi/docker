Main Hello World

'''
FROM openjdk:11
COPY Main.java .
RUN javac Main.java
CMD ["java", "Main"]
'''

docker build --no-cache -t nicolastvn/backend .
docker run -d --network=app-network -p 8081:8080 --name backend nicolastvn/backend