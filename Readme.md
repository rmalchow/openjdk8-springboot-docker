# Docker Image for springboot apps

#### Parameters

This is a simple base image for springboot applications. It includes a wrapper script at

    /assets/start.sh
    
this script takes the following parameters:

    -w HOST PORT TIMEOUT

this waits $TIMEOUT seconds for tcp services on $HOST:$PORT to become available. If that service does not become available, the script will exit. This option can be repeated multiple times.

    -o "additional_java_option"
    
this adds more parameters to the internal $JAVA_OPTS variable.

    -j /path/to/application.jar
    
this specifies the jar file to boot.

#### Environment

    JAVA_OPTS 
    
the default java options - defaults to -Djava.security.egd=file:/dev/urandom

    SECRETS_DIR

the directory to use for loading docker secrets - defaults to /run/secrets

#### Start

The start script will first perform the waiting for all services specified via "-w", then it will load all secrets from $SECRETS_DIR into environment variables, then it will invoke:

    java $JAVA_OPTS -jar $JAR

so, an example command in a docker image derived from this could look something like this:

    CMD /assets/start.sh -w $MYSQL_HOST $MYSQL_PORT 60 -o -Dfile.encoding=utf-8 -j /usr/share/app/my-app.jar
                            ^^^^ wait for mysql         ^^^^ set def encoding 

and the output should look something like:

	06/12/2017 16:43:50	waiting 60 seconds for tcp service: 172.19.23.10 3306
	06/12/2017 16:43:50	172.19.23.10|3306 available after 0 seconds
	06/12/2017 16:43:50	waiting 60 seconds for tcp service: rabbitmq 5672
	06/12/2017 16:43:50	rabbitmq|5672 available after 0 seconds
	06/12/2017 16:43:50	reading secrets ...
	06/12/2017 16:43:50	found secret: MYSQL_PASSWORD
	06/12/2017 16:43:51	2017-12-06 15:43:51.483  INFO 22 --- [           main] s.c.a.AnnotationConfigApplicationContext :  .....
	[ more spring boot logs ... ]




