# Some Important Docker Containers

## NodeJS

### Download 

```
docker pull sohailalam2/nodejs
```

### Usage

    docker run -it --rm dockerfile/nodejs

#### Run `node`

    docker run -it --rm dockerfile/nodejs node

#### Run `npm`

    docker run -it --rm dockerfile/nodejs npm
   
    
### Dockerfile

```
#
# Node.js Dockerfile
#
# https://github.com/dockerfile/nodejs
#

# Pull base image.
FROM dockerfile/python

# Install Node.js
RUN \
  cd /tmp && \
  wget http://nodejs.org/dist/node-latest.tar.gz && \
  tar xvzf node-latest.tar.gz && \
  rm -f node-latest.tar.gz && \
  cd node-v* && \
  ./configure && \
  CXX="g++ -Wno-unused-local-typedefs" make && \
  CXX="g++ -Wno-unused-local-typedefs" make install && \
  cd /tmp && \
  rm -rf /tmp/node-v* && \
  npm install -g npm && \
  echo -e '\n# Node.js\nexport PATH="node_modules/.bin:$PATH"' >> /root/.bashrc

# Define working directory.
WORKDIR /data

# Define default command.
CMD ["bash"]
```

-----------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------


## MongoDB

### Download 

```
docker pull sohailalam2/mongodb
```

### Usage

#### Run `mongod`

    docker run -d -p 27017:27017 --name mongodb dockerfile/mongodb

#### Run `mongod` w/ persistent/shared directory

    docker run -d -p 27017:27017 -v <db-dir>:/data/db --name mongodb dockerfile/mongodb

#### Run `mongod` w/ HTTP support

    docker run -d -p 27017:27017 -p 28017:28017 --name mongodb dockerfile/mongodb mongod --rest --httpinterface

#### Run `mongod` w/ Smaller default file size

    docker run -d -p 27017:27017 --name mongodb dockerfile/mongodb mongod --smallfiles

#### Run `mongo`

    docker run -it --rm --link mongodb:mongodb dockerfile/mongodb bash -c 'mongo --host mongodb'

##### Usage with VirtualBox (boot2docker-vm)

_You will need to set up nat port forwarding with:_  

    VBoxManage modifyvm "boot2docker-vm" --natpf1 "guestmongodb,tcp,127.0.0.1,27017,,27017"

This will allow you to connect to your mongo container with the standard `mongo` commands.

### Dockerfile

```
#
# MongoDB Dockerfile
#
# https://github.com/dockerfile/mongodb
#

# Pull base image.
FROM dockerfile/ubuntu

# Install MongoDB.
RUN \
  apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 7F0CEB10 && \
  echo 'deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen' > /etc/apt/sources.list.d/mongodb.list && \
  apt-get update && \
  apt-get install -y mongodb-org && \
  rm -rf /var/lib/apt/lists/*

# Define mountable directories.
VOLUME ["/data/db"]

# Define working directory.
WORKDIR /data

# Define default command.
CMD ["mongod"]

# Expose ports.
#   - 27017: process
#   - 28017: http
EXPOSE 27017
EXPOSE 28017
```

-----------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------


## Redis

### Download 

```
docker pull sohailalam2/redis
```

### Usage

#### Run `redis-server`

    docker run -d --name redis -p 6379:6379 dockerfile/redis

#### Run `redis-server` with persistent data directory. (creates `dump.rdb`)

    docker run -d -p 6379:6379 -v <data-dir>:/data --name redis dockerfile/redis

#### Run `redis-server` with persistent data directory and password.

    docker run -d -p 6379:6379 -v <data-dir>:/data --name redis dockerfile/redis redis-server /etc/redis/redis.conf --requirepass <password>

#### Run `redis-cli`

    docker run -it --rm --link redis:redis dockerfile/redis bash -c 'redis-cli -h redis'

### Dockerfile

```
#
# Redis Dockerfile
#
# https://github.com/dockerfile/redis
#

# Pull base image.
FROM dockerfile/ubuntu

# Install Redis.
RUN \
  cd /tmp && \
  wget http://download.redis.io/redis-stable.tar.gz && \
  tar xvzf redis-stable.tar.gz && \
  cd redis-stable && \
  make && \
  make install && \
  cp -f src/redis-sentinel /usr/local/bin && \
  mkdir -p /etc/redis && \
  cp -f *.conf /etc/redis && \
  rm -rf /tmp/redis-stable* && \
  sed -i 's/^\(bind .*\)$/# \1/' /etc/redis/redis.conf && \
  sed -i 's/^\(daemonize .*\)$/# \1/' /etc/redis/redis.conf && \
  sed -i 's/^\(dir .*\)$/# \1\ndir \/data/' /etc/redis/redis.conf && \
  sed -i 's/^\(logfile .*\)$/# \1/' /etc/redis/redis.conf

# Define mountable directories.
VOLUME ["/data"]

# Define working directory.
WORKDIR /data

# Define default command.
CMD ["redis-server", "/etc/redis/redis.conf"]

# Expose ports.
EXPOSE 6379
```

-----------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------



## CodeBox

### Download 

```
docker pull sohailalam2/codebox
```

### Usage

#### Step 1. Create SSH credentials (optional)

Create an `.ssh` directory for your codebox instance. This can go anywhere
but let's put it in a convenient place for this example. The codebox
server will run as `root`, so we must set the permissions on the
directory to be owned by `root`.

```bash
mkdir $HOME/codebox-ssh
chmod 700 $HOME/codebox-ssh
ssh-keygen -f $HOME/codebox-ssh/id_rsa
sudo chown -R root:root $HOME/codebox-ssh
```

#### Step 2. Run Codebox

Assuming the ssh credentials are in the directory above, you
can pass it to codebox when you run it.

```bash
docker run -p 8000:8000 -v $HOME/codebox-ssh:/root/.ssh:ro codebox run project
```

You should now be able to 
If you skipped Step 1, you can run it without passing `.ssh` credentials
without the `-v` flag.

```bash
docker run -p 8000:8000 codebox run project
```

To run with basic **user:password** authentication use the `-u` flag:
```bash
docker run -p 8000:8000 -v $HOME/codebox-ssh:/root/.ssh:ro codebox -u you@example.com:abc123 run project
```

For all other options see the help screen:

```bash
docker run codebox --help
```

### Dockerfile

```
FROM      ubuntu:14.04
MAINTAINER Kennon Kwok "kennon.kwok@gmail.com"

# install curl
RUN apt-get update && apt-get install -y npm git && apt-get clean && rm -fr /var/cache/apt

# install codebox
RUN ln -s /usr/bin/nodejs /usr/bin/node
RUN npm install -g codebox && npm cache clean

EXPOSE 8000
ENTRYPOINT ["codebox"]
```
