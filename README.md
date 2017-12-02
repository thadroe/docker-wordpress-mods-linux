### Docker Wordpress Modifications for Linux

Some modifications from the official Wordpress docker repo to suit my own needs developing locally on an Ubuntu host machine.

The problem: if using local volumes on a project directory, the files end up being owned by www-data, creating a bit of a permissions headache on a Linux host.

I primarily changed the user and group Apache runs as so it matches the username, group name, UID, and GID of the user on the host sytem, avoiding the need to constantly change permissions.

#### The main differences between this and official are

- Run apache as same UID and GID as user on the local host machine (If you'd like to use this, see below on what to change to match your own system)
- Includes wp-cli
- Support for SSL via self-signed cert

#### To build a local image of this for yourself

1. In the buildfiles directories of PHP and WP version you'd like to use, edit the Dockerfile and docker-entrypoint.sh files with your own username and UID on the following lines

In Dockerfile:

lines 43-45 and 65, change `user` and `1000` to match your own local username and UID

In docker-entrypoint.sh:

Lines 49 and 118, change `user` to your local username.

2. While in the directory the Dockerfile and docker-entrypoint.sh are in, run the build. Make sure docker-entrypoint.sh is executable with `chmod u+x docker-entrypoint.sh`.

Example build where name includes php version and tag contains wp version:

`sudo docker build -t="custom-wordpress-apache-php7.x:wp-4.x.x" .`

Change the name and tag to suit your preferences.

3. Check the image with `docker image ls`

To use it with docker-compose, see the corresponding compose files and modify them as you see fit.

For wp-cli you'll need to hop into the container and run it there with:

`docker-compose run wordpress /bin/bash`

Then change to the Apache user with:

`su user` (or whatever your username is)

4. Open your install

`http://localhost:8080`
`https://localhost:4430` 
`https://localhost:8000` for phpmyadmin