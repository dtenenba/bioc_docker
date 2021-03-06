# Linked Docker containers for developing the Bioconductor Support Site

Consists of four containers:

* Web app - this is a customized container. The rest are off-the-shelf from
 Docker Hub.
* Postgres container
* Elasticsearch container
* RabbitMQ container

[Docker Compose](https://docs.docker.com/compose/) is used to wire all these containers together.

To use:

* Install boot2docker if you are on 
  [Mac](https://docs.docker.com/installation/mac/) or
  [Windows](https://docs.docker.com/installation/windows/). This also
  installs Docker.
* [Install Docker](https://docs.docker.com/installation/) if you are
  on linux.
* [Install Docker Compose](https://docs.docker.com/compose/install/)

Then:

    git clone https://github.com/dtenenba/bioc_docker.git
    cd bioc_docker/support.bioconductor.org
    git clone https://github.com/Bioconductor/support.bioconductor.org.git \
        biostar-central

If you are running boot2docker, run `boot2docker start` and set
environment variables as suggested.

Then start the containers with:

    docker-compose up

(*NOTE*: `docker` and `docker-compose` commands may need to be preceded with `sudo`.)

When you see the line 

    web_1      | *** Run the development server with biostar.settings.base and DATABASE_NAME=biostar

Then the web app is running. You can connect to it as follows:

* Open another terminal window.
* If you are running boot2docker, run `. ../b2on` (see top-level[README](../#utilities))
  to set the appropriate environment variables and start boot2docker if necessary.
* Get the site's URL with `../get_url.sh`  
* You can log in with the email `0@foo.bar` and password `0@foo.bar`. This is an
  administrative user. You can also append `/admin` to the URL and log in
  to the admin webapp with this same account.
* Alternatively, log in as `nobody@bioconductor.org` with password `foobar`.


# NOTES

* These containers are for development only and should
  not be used for production. For one thing, the postgres
  container that is used is not very secure (although no 
  ports are open to it on the host).

# SOPs

## What to do when the search engine returns no results

The real solution is to figure out why this keeps happening and stop it from happening. Until then:

Restart elasticsearch - log in to support.bioconductor.org 
as ubuntu, then:

    sudo /etc/init.d/elasticsearch restart
    
Then log in as (or become) the www-data user on 
support.bioconductor.org and rebuild the search index:

    cd ~/biostar-central
    workon biostarsenv
    source live/custom.env
    ./biostar.sh index
    
## You have made changes; how to propagate them to the live site?

```
ssh www-data@support.bioconductor.org
# or ssh ubuntu@support.bioconductor.org and then:
# sudo su - www-data
cd biostar-central/
git pull
exit
ssh ubuntu@support.bioconductor.org
sudo apache2ctl restart
```

