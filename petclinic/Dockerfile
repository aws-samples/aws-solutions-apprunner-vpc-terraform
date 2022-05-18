FROM public.ecr.aws/amazonlinux/amazonlinux:latest
RUN yum install python3.7 -y && curl -O https://bootstrap.pypa.io/get-pip.py && python3 get-pip.py && yum update -y

VOLUME /tmp

COPY requirements.txt requirements.txt
RUN pip3 install -r requirements.txt
RUN opentelemetry-bootstrap --action=install
# Optional
ENV OTEL_PYTHON_DISABLED_INSTRUMENTATIONS=urllib3
# Optional
ENV OTEL_RESOURCE_ATTRIBUTES='service.name=petclinic'

ADD target/spring-petclinic-2.3.0.jar app.jar
EXPOSE 80
RUN yum -y install unzip

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && unzip awscliv2.zip && ./aws/install
RUN aws --version
RUN amazon-linux-extras enable corretto8 && yum -y install java-1.8.0-amazon-corretto
ENTRYPOINT OTEL_PROPAGATORS=xray OTEL_PYTHON_ID_GENERATOR=xray opentelemetry-instrument env -Dspring.datasource.password=$(aws ssm get-parameter --name /database/password --with-decrypt --region $AWS_REGION | grep Value | cut -d '"' -f4) -Dspring.datasource.username=$SPRING_DATASOURCE_USERNAME -Dspring.datasource.initialization-mode=$SPRING_DATASOURCE_INITIALIZATION_MODE -Dspring.profiles.active=$SPRING_PROFILES_ACTIVE -Dspring-boot.run.profiles=$SPRING_PROFILES_ACTIVE -Dspring.datasource.url=$SPRING_DATASOURCE_URL  java -Djava.security.egd=file:/dev/./urandom -jar /app.jar --debug