# EC2_ALB_WAF_SSL


Will cover 
- deploy java app on EC2
- Create ALB (LB,TG)
- Create WAF ACL (will block traffic for /listallcustomers URI path)
- ADD SSL to ALB

1. Run java app on EC2 instance

Install git/maven/java on EC2 instance.

    cd /opt
    yum install git -y

    sudo amazon-linux-extras enable corretto8
    sudo yum install java-1.8.0-amazon-corretto* -y

    wget https://dlcdn.apache.org/maven/maven-3/3.8.6/binaries/apache-maven-3.8.6-bin.zip
    unzip apache-maven-3.8.6-bin.zip 

    export PATH=$PATH:/opt/apache-maven-3.8.6/bin:.

Download Springboot app and run it:

    git clone https://github.com/tushardashpute/springboohello-CICD.git
    cd springboohello-CICD/
    mvn clean install
    cd target
    java -jar gs-spring-boot-0.1.0.jar

The java application will run on port 33333, so please make sure to 


    # java -jar gs-spring-boot-0.1.0.jar

      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::        (v2.2.2.RELEASE)

    2022-08-27 15:24:08.677  INFO 8692 --- [           main] org.example.App                          : Starting App v0.1.0 on ip-172-31-15-30.ap-south-1.compute.internal with PID 8692 (/opt/springboohello-CICD/target/gs-spring-boot-0.1.0.jar started by root in /opt/springboohello-CICD/target)
    2022-08-27 15:24:08.688  INFO 8692 --- [           main] org.example.App                          : No active profile set, falling back to default profiles: default
    2022-08-27 15:24:11.411  INFO 8692 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 33333 (http)
    2022-08-27 15:24:11.440  INFO 8692 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
    2022-08-27 15:24:11.441  INFO 8692 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.29]
    2022-08-27 15:24:11.597  INFO 8692 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
    2022-08-27 15:24:11.597  INFO 8692 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 2775 ms
    2022-08-27 15:24:13.087  INFO 8692 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
    2022-08-27 15:24:13.473  INFO 8692 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 33333 (http) with context path ''
    2022-08-27 15:24:13.484  INFO 8692 --- [           main] org.example.App                          : Started App in 5.78 seconds (JVM running for 6.735)


Once applitcation is started you can verify results:

<img width="1114" alt="image" src="https://user-images.githubusercontent.com/74225291/187036819-95450548-690e-4777-808d-8c77ac1f32da.png">

2. Create ABL (LB,TG)

goto EC2 --> LoadBalancer --> New Load Balancer --> Select load balancer type as "Application Load Balancer"

<img width="1176" alt="image" src="https://user-images.githubusercontent.com/74225291/187056655-1309896c-0c5f-4c2b-98a7-0bed7840e08d.png">

Select all the subnets where you are creating EC2 instances.

<img width="1215" alt="image" src="https://user-images.githubusercontent.com/74225291/187036987-b1bb45c7-2a16-4271-a252-177d47f13a4b.png">

<img width="1226" alt="image" src="https://user-images.githubusercontent.com/74225291/187037038-0542592a-ce83-410b-b44e-e9dad1c505cf.png">

Click on Create target group.

Select traget type as instances.

<img width="981" alt="image" src="https://user-images.githubusercontent.com/74225291/187037094-fa579fa9-3209-4a5d-ae99-f6df6399f871.png">

Click next

Select the EC@ instance where we have started java application and incluster it as pending below.

<img width="1286" alt="image" src="https://user-images.githubusercontent.com/74225291/187037128-f3f79632-ace6-434e-8acd-401597d5af2e.png">

Then click on create target group.Now select the newly created target group for the listner and click on create load balancer.

<img width="1302" alt="image" src="https://user-images.githubusercontent.com/74225291/187056851-7f80275d-3fa5-410a-8d58-c20bf7abf090.png">

<img width="1289" alt="image" src="https://user-images.githubusercontent.com/74225291/187056864-8cc756b7-4748-423b-aed6-a86b126d3127.png">

TG status:

<img width="1263" alt="image" src="https://user-images.githubusercontent.com/74225291/187056879-09c2d114-3b7a-4a8d-a7fd-493091a37aec.png">

Now select listener dns_name/listallcustomers and verify result if application is accessible over ALB.

<img width="1056" alt="image" src="https://user-images.githubusercontent.com/74225291/187057598-ca6f6ddd-3568-4c7b-b86b-3159bf7d9865.png">


3. Create WAF ACL 

Console --> Create web ACL --> 

<img width="1235" alt="image" src="https://user-images.githubusercontent.com/74225291/187057078-a8fd8bd1-f703-487b-94a3-db53356043b0.png">

Now click on Add AWS resource and select ALB which we created earlier.

<img width="936" alt="image" src="https://user-images.githubusercontent.com/74225291/187057133-966825da-ad5c-442f-8814-c6a92d96b1c7.png">

click Next

<img width="1262" alt="image" src="https://user-images.githubusercontent.com/74225291/187057177-ee8bc477-52b6-422e-89f9-4da872969c4f.png">

You can either use AWS managed rules(ddos/sql injection protection) or create your own rule. We will add rule to prevent traffic from URI path listallcustomers.

<img width="1223" alt="image" src="https://user-images.githubusercontent.com/74225291/187057234-67f98935-3c5e-415a-9095-6f79fbe677ca.png">

<img width="1161" alt="image" src="https://user-images.githubusercontent.com/74225291/187057245-958eaf6a-7c45-46cb-9322-f20100db10bc.png">

<img width="1190" alt="image" src="https://user-images.githubusercontent.com/74225291/187057254-e19e10ad-63b8-4fe1-8702-27938b84cbc8.png">

Now click on Add Rule. Keep everything as default --> next --> next --> Finally Review and create web ACL 

Click on ALB --> integrated services (You can see WAF is enabled for the ALB and ACL stop_listallcustomers is assigned to it)

<img width="1295" alt="image" src="https://user-images.githubusercontent.com/74225291/187056964-75fd5027-3ed7-4506-98ca-67563741bca3.png">

Now if we try to access the same URL it will be blocked.

<img width="1239" alt="image" src="https://user-images.githubusercontent.com/74225291/187056892-b317c7ae-aaef-472e-976c-bf0cd15cd3dc.png">

From WEB ACL monitoring we can see requests are blocked for listallcustomers endpoint.

<img width="1518" alt="image" src="https://user-images.githubusercontent.com/74225291/187057517-49b6713e-7a0b-4115-8704-92f5e9374fd1.png">

<img width="1272" alt="image" src="https://user-images.githubusercontent.com/74225291/187057553-a8ee494a-01d6-4a0e-bd1e-cc526c0cea47.png">

