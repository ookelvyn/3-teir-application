DEPLOYING 3-TIER APPLICATION
Nginx and Tomcat will run on Docker containers, while RDS will be application DB.
The web application will be accessed via nginx for security. 
---------------------------------------------
Step 1: 
Build the images for Tomcat, MySql and Nginx.
Create folder tomcat
- Create Dockerfile
- Copy the tomcat dockerfile content to the Dockerfile
- Build the docker file to to create an image
The Dockerfile contains a series of steps that define the build process for creating a Docker image. It seems to be aimed at setting up an environment for building and running a Java web application with Tomcat. Here are the steps in order:

1. **Build Stage (FROM amazonlinux AS build):**
   - Sets the base image to Amazon Linux.
   - Defines several environment variables (JAVA_HOME, PATH, and M2_HOME) for Java and Maven.
   - Installs necessary packages: wget, gzip, tar, and git.
   - Downloads and extracts OpenJDK 11 and moves it to /opt/jdk11.
   - Downloads and extracts Apache Maven and moves it to /opt/maven.
   - Creates a directory for SSH configuration and copies an SSH private key file to /root/.ssh/id_rsa.
   - Sets permissions and adds the Bitbucket host key to the known_hosts file.
   - Clones a Git repository (java-login-app) from Bitbucket to /opt/app.
   - Sets the working directory to /opt/app.
   - Builds the Java application using Maven.

2. **Final Stage (FROM amazonlinux):**
   - Sets the base image again to Amazon Linux (probably to keep the final image size minimal).
   - Defines the same Java-related environment variables as in the build stage.
   - Installs necessary packages: wget, gzip, tar, and git.
   - Downloads and extracts OpenJDK 11 and moves it to /opt/jdk11.
   - Downloads and extracts Apache Tomcat and moves it to /opt/tomcat.
   - Copies the built .war file (dptweb-1.0.war) from the build stage to /opt/tomcat/webapps/ in the final image.
   - Specifies the CMD to start Tomcat by running /opt/tomcat/bin/catalina.sh run.

The Dockerfile creates a two-stage Docker image. The first stage sets up the build environment, compiles a Java web application, and stores it as a .war file. The second stage uses a fresh Amazon Linux base image, installs Java, Tomcat, and other dependencies, and copies the compiled .war file into Tomcat's webapps directory. Finally, it configures the image to start Tomcat when a container is launched.


Step 2: 
Create a folder Nginx
- Create Dockerfile inside the folder.
- Copy the nginx dockerfile content to the Dockerfile
- Build the docker file to to create an image
The Dockerfile is designed to create a Docker image that installs and configures the Nginx web server on Amazon Linux. Here are the steps:

1. **Base Image (FROM amazonlinux):** The Docker image starts with the Amazon Linux as the base image.

2. **Install Telnet (RUN yum install telnet -y):** It installs the telnet package using the `yum` package manager with the `-y` option to automatically confirm any prompts for installation. However, this line is commented out with a "#" symbol, so it will not be executed when building the image.

3. **Install Nginx (RUN yum install nginx -y):** It installs the Nginx web server using `yum` with the `-y` option for automatic confirmation. This is the main step to set up Nginx.

4. **Copy Nginx Configuration (COPY ./nginx.conf /etc/nginx/nginx.conf):** It copies a local Nginx configuration file (nginx.conf) to the /etc/nginx/ directory within the Docker image. This step is used to replace the default Nginx configuration with a custom one. Make sure the nginx.conf file is available in the same directory as the Dockerfile.

5. **CMD ["nginx", "-g", "daemon off;"]:** This specifies the command to run when a container is started from the image. It starts Nginx with the "daemon off;" option, which keeps Nginx running in the foreground. This is commonly used when running Nginx within a Docker container.

The Dockerfile sets up an Amazon Linux-based Docker image, installs Nginx, replaces the default Nginx configuration with a custom one, and specifies the CMD to start Nginx with a particular configuration option. The installation of telnet is also included in the file but is currently commented out and won't be executed.

Step 3: Create a network
Create  network 
- docker create network networkName

Step 4: Run the tomcat and Nginx images on the created network
Run docker container for app on the network
- docker run --name appName -d -p 8080:8080 --network networkName tomcat:latest

Run docker container for nginx in the network 
- docker run --name webnginx -d -p 80:80 --network networkName nginx:latest