### Docker Wordpress Modifications for Linux

Some modifications from the official Wordpress docker repo to suit my own needs developing locally on an Ubuntu host machine.

The problem: if using local volumes on a project directory, the files end up being owned by www-data, creating a bit of a permissions headache on a Linux host.

I primarily changed the user and group Apache runs as so it matches the username, group name, UID, and GID of the user on the host sytem, avoiding the need to constantly change permissions.

#### The main differences between this and official Wordpress images on Docker Hub are:

- Run apache as same UID and GID as user on the local host machine (If you'd like to use this, see below on what to change to match your own system)
- Includes wp-cli
- Support for SSL via self-signed cert

#### To build a local image of this for yourself

1. In the buildfiles directories of PHP and WP version you'd like to use, edit the Dockerfile and docker-entrypoint.sh files with your own username and UID on the following lines

In Dockerfile:

lines 43-45 and 65, change `user` and `1000` to match your own local username and UID. You can find this by typing `id`.

In docker-entrypoint.sh:

Lines 49 and 118, change `user` to your local username.

2. While in the directory the Dockerfile and docker-entrypoint.sh are in, run the build. Make sure docker-entrypoint.sh is executable with `chmod u+x docker-entrypoint.sh`.

Example build where name includes php version and tag contains wp version:

`sudo docker build -t="custom-wordpress-apache-php7.x:wp-4.x.x" .`

Change the name and tag to suit your preferences.

3. Check that the image exists with `docker image ls`

#### To run with docker compose

1. Copy the corresponding **docker-compose.yml** file to your project directory and edit the following VIRTUAL_HOST line to whatever you'd like:

~~~~
VIRTUAL_HOST: mytestsite.local,www.mytestsite.local
~~~~

If you'd prefer to just use 'localhost', delete the VIRTUAL_HOST line

2. Run docker-compose to fire it up

`docker-compose up -d`

3. Open your install

http://mytestsite.local  
http://mytestsite.local:8000 for phpmyadmin

4. For wp-cli, you can run it with (changing 'user' for your own):

`docker-compose exec --user user wordpress wp`

To make that easier, you can create an alias in your ~/.bashrc file to something like:

~~~~
alias dcwp='docker-compose exec --user user wordpress wp'
~~~~

Then use 'dcwp' instead of 'wp'

You can run any wp-cli commands except for 'db' operations, but 'search-replace' still works.

---

**Note:** Only one docker-compose instance can be run at a time unless you change ports and adjust virtual hosts accordingly. See [https://github.com/jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy) if you'd like to explore some other possibilities of running multiple instances pumping through an nginx-proxy.