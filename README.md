# maven-java

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


## Spring Boot

### Set a component to be call  by using @Bean
```
@Component
public class S3Sample {
    private static final java.util.logging.Logger LOGGER = java.util.logging.Logger.getLogger(Application.class.getName());

    // Resolved because amazonS3 is setted in a @Configuration class
    @Autowired
    private AmazonS3 amazonS3;

    // Come from application.yml or Spring Vault 
    @Value("${s3.bucket.name}")
    private String s3BucketName;

    // @Bean => This class will be automaticaly called
    @Bean
    public void S3Sample()
    {

```

### Dependencies: Configuration => Service => RestController

* Configuration
```
@Configuration
public class FileConfiguration {
    ...
    ...
    @Bean
    public AmazonS3 getAmazonS3Client() {
      
        final BasicAWSCredentials basicAWSCredentials = new BasicAWSCredentials(accessKeyId, accessKeySecret);

        // Get Amazon S3 client and return the S3 client object
        return AmazonS3ClientBuilder
                .standard()
                .withCredentials(new AWSStaticCredentialsProvider(basicAWSCredentials))
                .withRegion(s3RegionName)
                .build();
    }
```
* Service 
```

@Service
public class FileService {

    private static final Logger LOG = LoggerFactory.getLogger(FileService.class);

    // Setted in the @Configuration
    @Autowired
    private AmazonS3 amazonS3;

    @Value("${s3.bucket.name}")
    private String s3BucketName;
    
    // @Async annotation ensures that the method is executed in a different thread
    @Async
    public S3ObjectInputStream findByName(String fileName) {
        LOG.info("Downloading file with name {}", fileName);
        return amazonS3.getObject(s3BucketName, fileName).getObjectContent();
    }

```

* RestController
```
// Import the Service
import com.hawksys.s3.FileService;
...

@RestController
@RequestMapping("/api/v1/files")
@CrossOrigin(origins = "*", maxAge = 3600)
public class FileController {

    private static final String MESSAGE_1 = "Uploaded the file successfully";
    private static final String FILE_NAME = "fileName";

    // Setted in the @Service
    @Autowired
    FileService fileService;

    // Call the method in findByName in the Service
    @GetMapping
    public ResponseEntity<Object> findByName(@RequestBody(required = false) Map<String, String> params) {
        return ResponseEntity
                .ok()
                .cacheControl(CacheControl.noCache())
                .header("Content-type", "application/octet-stream")
                .header("Content-disposition", "attachment; filename=\"" + params.get(FILE_NAME) + "\"")
                .body(new InputStreamResource(fileService.findByName(params.get(FILE_NAME))));

    }

```

