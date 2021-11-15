# maven

## Installation
```
sudo apt update
sudo apt install maven
```

## Usage
```
mvn package
mvn install

# depencency
mvn dependency:resolve 
mvn dependency:tree

# build
mvn clean install

# skip tests
mvn clean install -DskipTests=true

mvn clean install -DskipTests=true -Dmaven.wagon.http.ssl.insecure=true

java -jar target/springboot-swagger-test-0.0.1-SNAPSHOT.jar
```
