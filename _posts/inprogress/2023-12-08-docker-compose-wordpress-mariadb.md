---

layout: post

author: softwareshinobi

title:  "wordpress / mariadb / docker compose"

categories: [ Websites, WordPress ]

image: assets/images/softwareshinobi/html/docker/transform-html-website-docker-image.png

description: "In this docker guide you'll learn how to transform your HTML source code into a Docker image using a Dockerfile and Docker Compose."

tags: [sticky,featured,guide,html,how-to,docker,docker image,docker compose,]

hidden: false

---

services:

##########################################
##
## valorant digital networking
##
##########################################

    valorant-digital-proxy:

        container_name: valorant-digital-proxy

        image: nginxproxy/nginx-proxy

        restart: unless-stopped

        ports:

            - 80:80

            - 443:443

        volumes:

            - /var/run/docker.sock:/tmp/docker.sock:ro

            - /var/docker/nginx/html:/usr/share/nginx/html

            - /var/docker/nginx/certs:/etc/nginx/certs

            - /var/docker/nginx/vhost:/etc/nginx/vhost.d

        logging:

            options:

                max-size: "10m"

                max-file: "3"

    valorant-digital-letsencrypt:

        container_name: valorant-digital-letsencrypt

        image: jrcs/letsencrypt-nginx-proxy-companion

        restart: unless-stopped

        volumes_from:

            - valorant-digital-proxy

        volumes:

            - /var/run/docker.sock:/var/run/docker.sock

            - /var/docker/nginx/acme:/etc/acme.sh

        environment:

            DEFAULT_EMAIL: the.software.shinobi@gmail.com

##########################################
##
## software shinobi / softwareshinobi.digital
##
##########################################

    software-shinobi-wordpress:

        container_name: software-shinobi-wordpress

        image: wordpress:latest

        restart: unless-stopped

        depends_on:

            - software-shinobi-database

        environment:

            WORDPRESS_DB_HOST: software-shinobi-database:3306

            WORDPRESS_DB_USER: agent_eight

            WORDPRESS_DB_PASSWORD: agent_eight

            WORDPRESS_DB_NAME: agent_eight

            VIRTUAL_HOST: softwareshinobi.digital

            LETSENCRYPT_HOST: softwareshinobi.digital

        ports:

            - 8888:80

        volumes:

            -  .volumes/wordpress:/var/www/html

    software-shinobi-database:

        container_name: software-shinobi-database

        image: mariadb:latest

        restart: unless-stopped

        environment:

            MYSQL_ROOT_PASSWORD: aggiepride

            MYSQL_DATABASE: agent_eight

            MYSQL_USER: agent_eight

            MYSQL_PASSWORD: agent_eight

        volumes:

            -  .volumes/database:/var/lib/mysql

    software-shinobi-phpmyadmin:

        container_name: software-shinobi-phpmyadmin

        image: beeyev/phpmyadmin-lightweight:latest

        restart: unless-stopped

        links:

            - software-shinobi-database

        ports:

            - 8889:80
  
        environment:

            PMA_HOST: software-shinobi-database

            PMA_PORT: 3306

            PMA_ARBITRARY: 1

            VIRTUAL_HOST: database.softwareshinobi.digital

            LETSENCRYPT_HOST: database.softwareshinobi.digital

