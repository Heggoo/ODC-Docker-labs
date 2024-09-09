FROM ubuntu:focal

# Update packages and install nginx
WORKDIR /
RUN apt-get -y update \
    && apt-get -y install nginx

# Create a .tar file containing index.html
RUN tar -cvf /var/www/html/index.tar -C /var/www/html index.nginx-debian.html

# RUN service nginx start
CMD ["nginx", "-g", "daemon off;"]

EXPOSE 80