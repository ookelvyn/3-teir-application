FROM amazonlinux
RUN yum install telnet -y
# RUN amazon-linux-extras install nginx1 -y
RUN yum install nginx -y
COPY ./nginx.conf /etc/nginx/nginx.conf
CMD ["nginx", "-g", "daemon off;"]
