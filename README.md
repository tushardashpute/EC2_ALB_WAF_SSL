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

<img width="1286" alt="image" src="https://user-images.githubusercontent.com/74225291/187037321-6adf0969-1ab6-4e90-92f8-774c8df39449.png">

<img width="1223" alt="image" src="https://user-images.githubusercontent.com/74225291/187037267-33f23f9e-0736-4fa6-aec0-93236b0f4440.png">

TG status:

<img width="1286" alt="image" src="https://user-images.githubusercontent.com/74225291/187037360-e87a8dac-2b35-4d41-895f-111ebd6ab32e.png">

Now select listener dns_name/listallcustomers and verify result if application is accessible over NLB.

<img width="1092" alt="image" src="https://user-images.githubusercontent.com/74225291/187036863-60d93515-490a-4958-abf9-1d2d199fa02d.png">
