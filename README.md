# Webserver on Docker containers.

A description of the creation process is available at **[habr.com](https://habr.com/ru/post/670938/)**.

The composition includes:
- MySQL
-PHP
- nginx
- msmtp
- composer
- letsencrypt SSL certificates
- backup to the cloud

## Before starting work

1. **[Prepare](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-22-04)** server
2. **[Install](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)** **docker**
3. **[Install](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-docker-compose)** **docker-compose**

## Installation order:

### 1. Clone the repository

~~~
git clone git@github.com:a-kryvenko/docker-website.git .
~~~

### 2. Copy the file with environment variables:

~~~
cp .env.example .env
~~~

### 3. Specify values ​​for variables in the .env file

<details>
    <summary><b>Description of variables</b></summary>
    <ul>
        <li><b>COMPOSE_FILE</b> - which docker-compose files to include. Differ for dev and production environments;</li>
        <li><b>SYSTEM_GROUP_ID</b> - group ID of the host user on behalf of which we are working with the server. Usually 1000;</li>
        <li><b>SYSTEM_USER_ID</b> - group ID of the host user on behalf of which we are working with the server. Usually 1000;</li>
        <li><b>APP_NAME</b> - <b>url</b> by which the site is accessible. For example, <b>example.com</b> or <b>example.local</b> for local development;</li>
        <li><b>ADMINISTRATOR_EMAIL</b> - email to which we send information about certificates;</li>
        <li><b>DB_HOST</b> - database host. By default <b>db</b>, but in the case when the database is on another server - specify the server address;</li>
        <li><b>DB_DATABASE</b> - database name;</li>
        <li><b>DB_USER</b> - the name of the user who works with the database;</li>
        <li><b>DB_USER_PASSWORD</b> - database user password;</li>
        <li><b>DB_ROOT_PASSWORD</b> - <b>root</b> password of the database user;</li>
        <li><b>AWS_S3_URL</b> - <b>url</b> of cloud backup storage;</li>
        <li><b>AWS_S3_BUCKET</b> - name of the bucket in the backup storage;</li>
        <li><b>AWS_S3_ACCESS_KEY_ID</b> - storage key;</li>
        <li><b>AWS_S3_SECRET_ACCESS_KEY</b> - storage password;</li>
        <li><b>AWS_S3_LOCAL_MOUNT_POINT</b> - path to the local folder where we mount the cloud storage;</li>
        <li><b>MAIL_SMTP_HOST</b> - smpt host for sending mail, e.g. <b>smtp.gmail.com</b>;</li>
        <li><b>MAIL_SMTP_PORT</b> - smpt port. Default 25;</li>
        <li><b>MAIL_SMTP_USER</b> - smpt username;</li>
        <li><b>MAIL_SMTP_PASSWORD</b> - smtp password.</li>
    </ul>
</details>

Separately, it is worth mentioning **COMPOSE_FILE**. Depending on the environment
we are launching a website - we need different services. For example, locally - to us
you only need a base and a cloud for backups:

> compose-app.yml:compose-cloud.yml

For **dev** site - backups and https:

> compose-app.yml:compose-https.yml:compose-cloud.yml

For **production** - the whole set:
> compose-app.yml:compose-https.yml:compose-cloud.yml:compose-production.yml

### 4. Collect images and start our server

~~~
docker-compose build\
docker-compose up -d
~~~

### 5. If we use https, then run the script to get certificates

~~~
./cgi-bin/prepare-certbot.sh
~~~

### 6. Initialize crontab

~~~
./cgi-bin/prepare-crontab.sh
~~~