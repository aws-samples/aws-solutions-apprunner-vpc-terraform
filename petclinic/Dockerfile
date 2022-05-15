FROM public.ecr.aws/bitnami/java:latest
VOLUME /tmp

RUN pip3 install -r requirements.txt
RUN opentelemetry-bootstrap --action=install
ENV OTEL_PYTHON_DISABLED_INSTRUMENTATIONS=urllib3 // Optional 
ENV OTEL_RESOURCE_ATTRIBUTES='service.name=petclinic' // Optional 

ADD target/spring-petclinic-2.3.0.jar app.jar
EXPOSE 80
RUN apt-get update
RUN apt-get -y install awscli
ENTRYPOINT OTEL_PROPAGATORS=xray OTEL_PYTHON_ID_GENERATOR=xray opentelemetry-instrument env spring.datasource.password=$(aws ssm get-parameter --name /database/password --with-decrypt --region $AWS_REGION | grep Value | cut -d '"' -f4) java -Djava.security.egd=file:/dev/./urandom -jar /app.jar


