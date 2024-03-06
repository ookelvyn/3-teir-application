FROM amazonlinux AS build
ENV JAVA_HOME /opt/jdk11
ENV PATH $PATH:/opt/jdk11/bin
ENV M2_HOME /opt/maven
RUN export PATH
RUN export JAVA_HOME
RUN yum install wget -y && \
    yum install gzip -y && \
    yum install tar -y && \
    yum install git -y
RUN wget https://download.java.net/openjdk/jdk11.0.0.1/ri/openjdk-11.0.0.1_linux-x64_bin.tar.gz && \
    tar zxf openjdk-11.0.0.1_linux-x64_bin.tar.gz && \
    mv jdk-11.0.0.1 /opt/jdk11

RUN wget https://dlcdn.apache.org/maven/maven-3/3.8.8/binaries/apache-maven-3.8.8-bin.tar.gz && \
    tar zxf apache-maven-3.8.8-bin.tar.gz && \
    mv apache-maven-3.8.8 /opt/maven

RUN mkdir /root/.ssh
COPY ./id_rsa /root/.ssh/id_rsa

RUN chmod -R 700 /root/.ssh && \
    chown -R root:root /root/.ssh && \
    chmod 600 /root/.ssh/id_rsa && \
    ssh-keyscan bitbucket.org >> /root/.ssh/known_hosts

RUN git clone git@bitbucket.org:ookelvin/java-login-app.git /opt/app
WORKDIR /opt/app
RUN /opt/maven/bin/mvn package

FROM amazonlinux
ENV JAVA_HOME /opt/jdk11
ENV PATH $PATH:/opt/jdk11/bin
RUN yum install wget -y && \
    yum install gzip -y && \
    yum install tar -y && \
    yum install git -y
RUN wget https://download.java.net/openjdk/jdk11.0.0.1/ri/openjdk-11.0.0.1_linux-x64_bin.tar.gz && \
    tar zxf openjdk-11.0.0.1_linux-x64_bin.tar.gz && \
    mv jdk-11.0.0.1 /opt/jdk11
RUN wget https://dlcdn.apache.org/tomcat/tomcat-8/v8.5.95/bin/apache-tomcat-8.5.95.tar.gz && \
    tar zxf apache-tomcat-8.5.95.tar.gz && \
    mv apache-tomcat-8.5.95 /opt/tomcat

COPY --from=build /opt/app/target/dptweb-1.0.war /opt/tomcat/webapps/
CMD ["/opt/tomcat/bin/catalina.sh", "run"]