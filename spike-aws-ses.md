**SES information**
Server Name:	
email-smtp.us-west-2.amazonaws.com
Port:	25, 465 or 587
Use Transport Layer Security (TLS):	Yes
Authentication:	Your SMTP credentials. See below for more information.
Access key: AKIAYMIQGBRBA27HW64U
Secret access key: GgZpW5iKH8n21Mla5EHNOsEEH5DQvDIg9gFph7X1

Verified sender email: gridsoladmin@lsis.com

please refer below link
https://us-west-2.console.aws.amazon.com/ses/home?region=us-west-2#smtp-settings:
(login by skshon@lsis.com)

======================================================================================
======================================================================================

### Recipe -- Use AWS SES sending email by AWS SDK v2

This recipe introduce how to use AWS SDK v2 SesClient to send email through AWS Simple Email Service. You need go through below steps.

##### 1. register AWS account, create IAM account and assign SES permissions

Before you can use AWS SES, you should have an AWS account at first. So please go to [https://aws.amazon.com](https://aws.amazon.com/) register AWS account if you haven't.

Then you need to create an IAM user and assign SES permissions to it. You also can assign SES permissions to en exsiting IAM user of course. Below is the steps.

1. Login in `AWS Management Console`, find `IAM` service and enter it.
2. Open `Users` subpage, and click `Add user` button, fill name, select `Programmatic access` as Access type, then next.
3. select `Attach existing policies directly`, then input `ses` in filter area, then select `AmazonSESFullAccess`, then next and next, and then create user.
4. **Important**, before you click `close` button, don't forget  download csv or write down the secret access key.



##### 2. Verify sender email address 

At the first time, your SES account just has `sandbox` access, which means both sender and receiver's email address need to be verified at first. After moving out from sandbox, then only sender email address need to be verified. 

To verify email address, you go to `AWS Management Console` , find `Simple Email Service` and enter it. Then open `Email Addresses` subpage, and start to verify email addresses. You can test it after verification.

(Optional) Refer this link to [moving out from sandbox](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html)



##### 3. config AWS credentials for running locally

Set the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables.

To set these variables on Linux, macOS, or Unix, use **export** :

```
export AWS_ACCESS_KEY_ID=AKIAYMIQGBRBA27HW64U
export AWS_SECRET_ACCESS_KEY=GgZpW5iKH8n21Mla5EHNOsEEH5DQvDIg9gFph7X1
```

To set these variables on Windows, use **set** :

```
set AWS_ACCESS_KEY_ID=your_access_key_id
set AWS_SECRET_ACCESS_KEY=your_secret_access_key
```



##### 4. create Spring boot app and add AWS SDK dependency

Gradle:

```groovy
dependencies {
    //......
    implementation platform('software.amazon.awssdk:bom:2.5.29')
    implementation 'software.amazon.awssdk:ses'
	//......
}
```

Maven:

```xml
<dependencies>
    <dependency>
        <groupId>software.amazon.awssdk</groupId>
        <artifactId>ses</artifactId>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>software.amazon.awssdk</groupId>
            <artifactId>bom</artifactId>
            <version>2.5.29</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```



##### 5. write code to config SES client

```java
@Configuration
public class HellosesConfig {

    @Bean
    public SesClient sesClient() {
        SesClient sesClient = SesClient.builder().region(Region.US_WEST_2).build();
        return sesClient;
    }
}
```



##### 6. write code to send email 

```java
@Service
public class HellosesService {

    @Autowired
    private SesClient sesClient;

    public String sendEmail(String fromAddr, String toAddr, String subjectStr, String htmlBody, String textBody) {

        SendEmailRequest request = SendEmailRequest.builder()
                .source(fromAddr)
                .destination(Destination.builder().toAddresses(toAddr).build())
                .message(Message.builder()
                        .body(Body.builder()
                                .html(Content.builder().charset("UTF-8").data(htmlBody).build())
                                .text(Content.builder().charset("UTF-8").data(textBody).build()).build())
                        .subject(Content.builder().charset("UTF-8").data(subjectStr).build()).build())
                .build();
        SendEmailResponse response = sesClient.sendEmail(request);
        return response.sdkHttpResponse().statusText().orElse("NULL");
    }
}
```



##### 7. test

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class HellosesApplicationTests {

    // Replace sender@example.com with your "From" address.
    // This address must be verified with Amazon SES.
    static final String FROM = "jipeng@pivotal.io";

    // Replace recipient@example.com with a "To" address. If your account
    // is still in the sandbox, this address must be verified.
    static final String TO = "jianhuap@qq.com";

    // The subject line for the email.
    static final String SUBJECT = "Amazon SES test (AWS SDK for Java)";

    // The HTML body for the email.
    static final String HTMLBODY = "<h1>Amazon SES test (AWS SDK for Java)</h1>"
            + "<p>This email was sent with <a href='https://aws.amazon.com/ses/'>"
            + "Amazon SES</a> using the <a href='https://aws.amazon.com/sdk-for-java/'>"
            + "AWS SDK for Java</a>";

    // The email body for recipients with non-HTML email clients.
    static final String TEXTBODY = "This email was sent through Amazon SES "
            + "using the AWS SDK for Java.";

    @Autowired
    private HellosesService hellosesService;

    @Test
    public void test_sendEmail() {
        String result = hellosesService.sendEmail(FROM, TO, SUBJECT, HTMLBODY, TEXTBODY);
        assertThat(result).isEqualTo("OK");
    }
}
```



##### 8. config AWS credentials for running on pcf

You have two method to set `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` env variables for app running on PCF. 

1. Set them in manifest.yml as below

   ```yaml
   ---
   applications:
   - name: helloses
     path: build/libs/helloses.jar
     env:
       AWS_ACCESS_KEY_ID: your_access_key_id
   	AWS_SECRET_ACCESS_KEY: your_secret_access_key
   ```

   

2. Set them by cf cli manually

   ```shell
   cf set-env APP_NAME ENV_VAR_NAME ENV_VAR_VALUE
   ```

   

