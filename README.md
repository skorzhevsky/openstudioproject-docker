# How to run [OpenStudio](https://www.openstudioproject.com/) yoga software in the Docker.

# WARNING: in my env works unstable, web2py just dies time to time.

 1. Install [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/).
 2. Install Docker [Compose](https://docs.docker.com/compose/install/).
 3. Create directory structure (you can adjust):
     * mkdir /root/docker
     * cd /root/docker
     * git clone https://github.com/skorzhevsky/openstudioproject-docker.git.
 4. Create MySQL database: run MySQL container in interactive mode (you can change passwords):
```sh
    docker run -it --rm --name yoga-mysql -v /root/docker/openstudioproject-docker/data/mysql:/var/lib/mysql -e MYSQL_USER=yoga -e MYSQL_PASSWORD=pwd1 -e MYSQL_DATABASE=openstudio_db -e MYSQL_ROOT_PASSWORD=pwd2 mysql:5.7 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```
5. When MySQL successfully started and created database/user, stop container by pressing "ctrl+\\".
6. Download [web2py](https://github.com/web2py/web2py/releases). 2.17.7 at the time of writing.
7. Unpack it into /root/docker/openstudioproject-docker/data/. The directory will be web2py-R-2.17.1.
8. Download [OpenStudio](https://github.com/openstudioproject/openstudio/releases). 2018.81.0 at the time of writing.
9. Unpack into /root/docker/openstudioproject-docker/data/web2py-R-2.17.1/applications. You can rename folder to "yogastudio" for simplicity.
10. Update  yogastudio/private/appconfig.ini:
```sh
[db]
uri = mysql://yoga:pwd1@ym/openstudio_db
...
[cache]
cache = redis
redis_host = yr
``` 
11. Create/adjust *routes.py* according to recommendations [OpenStudio install
](https://openstudio-docs.readthedocs.io/en/latest/installation.html)
12. If you don't need SSL, run:
```sh
./copy-nossl.sh
```
to copy Dockerfile-nossl to Dockerfile and docker-compose.yml-nossl to docker-compose.yml.

13. If you need SSL support, run
```sh
./copy-ssl.sh
```
to copy Dockerfile-ssl to Dockerfile and docker-compose.yml-ssl to docker-compose.yml, then create [certificate](https://letsencrypt.org/) (or self-signed) and update Dockerfile  and docker-compose.yml accordingly. 

14. Build image:
```sh
./cr_image.sh
```
15. Start applications (port 8000 for nossl or 8443 for ssl):
```sh
./start.sh
```
16. Open browser on http://host:8000 or https://host:8443. Default user/password is admin@openstudioproject.com/OSAdmin1# (taken from OpenStudio [docs](https://openstudio-docs.readthedocs.io/en/latest/installation.html))
17. Stop, if needed:
```sh
./stop.sh
```
