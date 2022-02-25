# maven

## Installation
```
sudo apt update
sudo apt install maven
```

## Usage
```
mvn archetype:generate -DgroupId=com.hawksys.s3 -DartifactId=s3-demo -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false

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
