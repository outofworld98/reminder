*After the spike, we summary out below receipe*

### Recipe -- Using MongoDB



#### Installation

To install MongoDB for dev, download MongoDB Community Server from <https://www.mongodb.com/download-center/community> at first, and then install it.

To install MongoDB client GUI, I recommend RoboMongo(Robo 3T) which support all OSs, download it from <https://robomongo.org/> and then install it.



#### Mongo Dependency

When create spring boot app which using mongoDB, you need select `Spring Data MongoDB` and `Embedded MongoDB`. The embedded MongoDB is just for testing. 

Maven: pom.xml looks like below.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    <dependency>
        <groupId>de.flapdoodle.embed</groupId>
        <artifactId>de.flapdoodle.embed.mongo</artifactId>
        <scope>test</scope>
    </dependency>
    ......
</dependencies>
```

Gradle : build.gradle looks like below.

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-mongodb'
    testImplementation 'de.flapdoodle.embed:de.flapdoodle.embed.mongo'
}
```

#### Mongo Java Configuration

Before using MongoTemplate or MongoRepository, you need configure  mongo at first. We need configure it for three environments separately.

**For local env, we use local mongoDB**, like below

```java
@Profile("local")
@Configuration
@EnableMongoRepositories(basePackages = "com.lsis.core")
public class MongoConfig extends AbstractMongoConfiguration {

    @Value("${local.mongodb.host}")
    private String host;

    @Value("${local.mongodb.port}")
    private int port;

    @Value("${local.mongodb.database}")
    private String database;

    @Override
    public MongoClient mongoClient() {
        return new MongoClient(host, port);
    }

    @Override
    protected String getDatabaseName() {
        return database;
    }
}
```
You also need activate profile in build.gradle file like this;
```groovy
bootRun {
    bootRun.systemProperty 'spring.profiles.active', "local"
}
```

**Fot test env, we use embedded mongodb.**

Since we already added `Embedded MongoDB` dependency, spring applcation context will load configuration automatically, so we just need use @DataMongoTest to test.

**For PCF env, we use mongoDB service in PCF.**

After the application bind to mongoDB service, then application will retrieve configuration from VCAP_SERVICE variables automatically. We do not need do anything for configuration.

#### Define Document Class

Just like entity in JPA, we need define Document class before save data into mongoDB.

```java
@Document(collection = "userCol")
public class User {

	@Id //document id
	private String id;
	
    @Indexed //create index for this field
    private String name;
    
    @Field("email") //field name change to 'email' instead of 'eamilAddress'
	private EmailAddress emailAddress;
     
    @Transient //this feild will not store into mongoDB
    private Integer yearOfBirth;
}
```



#### Using MongoTemplate

##### 1. Save, insert new one or update old one.

```java
User user = new User();
user.setName("Jared"); 
mongoTemplate.save(user, "userCol2"); //save to collection "userCol2", which override @Document(collection = "userCol")
mongoTemplate.save(user); //save to collection "userCol", which defined in @Document
```

##### 2. UpdateFirst and UpdateMulti

```java
Query query = new Query();
query.addCriteria(Criteria.where("name").is("Jared"));
Update update = new Update();
update.set("name", "Steven");

//update the first document
mongoTemplate.updateFirst(query, update, User.class);

//update all matched documents
mongoTemplate.updateMulti(query, update, User.class);
```

##### 3. Query

```java
//is
Query query = new Query();
query.addCriteria(Criteria.where("name").is("Jared"));
List<User> users = mongoTemplate.find(query, User.class);

//Regex, start with
Query query = new Query();
query.addCriteria(Criteria.where("name").regex("^A"));
List<User> users = mongoTemplate.find(query,User.class);

//Regex, end with
Query query = new Query();
query.addCriteria(Criteria.where("name").regex("c$"));
List<User> users = mongoTemplate.find(query, User.class);

//less than, great than
Query query = new Query();
query.addCriteria(Criteria.where("age").lt(50).gt(20));
List<User> users = mongoTemplate.find(query,User.class);

//sort
Query query = new Query();
query.with(new Sort(Sort.Direction.ASC, "age"));
List<User> users = mongoTemplate.find(query,User.class);
```

##### 4. Remove

```java
mongoTemplate.remove(user, "user");
```



#### Using MongoRepository

First, you need define repository interface as below.

```java
public interface UserRepository extends MongoRepository<User, String> {
    List<User> findByName(String name);
    List<User> findByNameStartingWith(String regexp);
    List<User> findByNameEndingWith(String regexp);
    List<User> findByAgeBetween(int ageGT, int ageLT);
    List<User> findByNameLikeOrderByAgeAsc(String str);
    
    @Query("{ 'name' : ?0 }")
	List<User> findUsersByName(String name);
    
    @Query("{ 'name' : { $regex: ?0 } }")
	List<User> findUsersByRegexpName(String regexp);
    
    @Query("{ 'age' : { $gt: ?0, $lt: ?1 } }")
	List<User> findUsersByAgeBetween(int ageGT, int ageLT);
}
```

##### 1. Save, insert new one or update old one

```java
User user = new User();
user.setName("Aaron");
userRepository.save(user);
```

##### 2. Query

```java
//FindByX
List<User> users = userRepository.findByName("Jared");

//StartingWith and endingWith
List<User> users = userRepository.findByNameStartingWith("A");
List<User> users = userRepository.findByNameEndingWith("c");

//Between
List<User> users = userRepository.findByAgeBetween(20, 50);

//Like and OrderBy
List<User> users = userRepository.findByNameLikeOrderByAgeAsc("A");

//@Query
List<User> users = userRepository.findUsersByName("Eric");
List<User> users = userRepository.findUsersByRegexpName("^A");
List<User> users = userRepository.findUsersByRegexpName("c$");
List<User> users = userRepository.findUsersByAgeBetween(20, 50);
```

##### 3. Delete

```
userRepository.delete(user);
```



#### Testing with embedded MongoDB

Since we already have `Embedded MongoDB` dependency in pom.xml, the mongoTemplate or mongoRepository will be created automatically, you can use them for test, as below.

```java
@RunWith(SpringRunner.class)
@DataMongoTest
public class PmongoTests {

	@Autowired
    MongoTemplate mongoTemplate;

    @Test
    public void testMongoTemplate() {
        // given
        DBObject objectToSave = BasicDBObjectBuilder.start().add("key", "value").get();

        // when
        mongoTemplate.save(objectToSave, "collection");
        
        // then
        assertThat(mongoTemplate.findAll(DBObject.class, "collection")).extracting("key")
                .containsOnly("value");
        assertThat(mongoTemplate.findAll(User.class).extracting("key")
                .containsOnly("value");
    }
                   
    @Test
    public void testMongoRepository(){
        FastData fastData = new FastData();
        fastData.setDeviceId("pcs-300");

        helloworldRepository.deleteAll();

        helloworldRepository.save(fastData);
        List<FastData> list = helloworldRepository.findAll();
        assertThat(list.get(0).getDeviceId()).isEqualTo("pcs-300");

    }
}
```



#### Transaction

You also can use MongoTransactionManger and @Transactional to implement transaction for MondoDB operations.

First, you need configure MongoTransactionManger

```java
@Configuration
@EnableMongoRepositories(basePackages = "com.tiehuapen.repository")
public class MongoConfig extends AbstractMongoConfiguration {
  
    @Override
    public MongoClient mongoClient() {
        return new MongoClient("127.0.0.1", 27017);
    }
    
    @Override
    protected String getDatabaseName() {
        return "test";
    }
    
    @Bean
    MongoTransactionManager transactionManager(MongoDbFactory dbFactory) {
        return new MongoTransactionManager(dbFactory);
    }
}
```

After that, you can use @Transactional on java method which include multi mongoDB operations.

```java
@Transactional
public void saveTwoUsers() {
    userRepository.save(new User("Jared", 20));
    userRepository.save(new User("Kweeny", 18));
}
```



#### Using MongoDB Service in PCF

If there is no MongoDB service in PCF marketplace, you need create an user-provided service and then bind it to your app, just as below. You also can configure it by PCF APP Manager UI.

```shell
cf create-user-provided-service <my-mongo-db> -p '{"uri": "mongodb://$username:$password@$hostname:$port/$db"}'
cf bind-service <app-name> <my-mongo-db>
cf restage <app-name>
```

If your app just bind only one mongoDB service, you can use `MongoTemplate` or `MongoRepository` in your java code directly, no need manual configuration, since `Spring Cloud Spring Service Connector` will configure it automatically.
