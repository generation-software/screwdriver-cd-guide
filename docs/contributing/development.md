---
layout: main
title: Development
category: Contributing
menu: menu
toc:
  - title: Prerequisites
    url: "#prerequisites"
    active: true
  - title: Setup
    url: "#setup"
  - title: Setting up Executor Queue and Queue Service
    url: "#setting-up-executor-queue-and-queue-service"
  - title: Using Custom Launcher
    url: "#using-custom-launcher"
---

Development
===========

Prerequisites
-------------

- [Node](https://nodejs.org/) v18.0.0 or higher
- [Docker](https://www.docker.com/products/docker-desktop)

Setup
-----

### Mapping sd.screwdriver.cd to local IP

Append this line to our local `/etc/hosts` file:

```bash
127.0.0.1 sd.screwdriver.cd
```

### Creating a New GitHub OAuth Application

Go to **Settings** > **Developer settings** > **OAuth Apps**, click **New OAuth App** button and configure as described
below:

- Application Name: (choose for yourself)
- Homepage URL: `http://sd.screwdriver.cd:4200`
- Application description: (choose for yourself)
- Authorization callback URL: `http://sd.screwdriver.cd:9001/v4/auth/login`

See screenshot below

![developing-locally-ouath](../cluster-management/assets/developing-locally-ouath.png)

> Take note of the client ID and the client Secret, we'll need them in the following step

### Getting Source Codes

* [ui](https://github.com/QubitPi/screwdriver-cd-ui)
* [screwdriver](https://github.com/QubitPi/screwdriver-cd-screwdriver)
* [store](https://github.com/QubitPi/screwdriver-cd-store)
* [queue-service](https://github.com/QubitPi/screwdriver-cd-queue-service)

```bash
git clone https://github.com/QubitPi/screwdriver-cd-ui.git
git clone https://github.com/QubitPi/screwdriver-cd-screwdriver.git
git clone https://github.com/QubitPi/screwdriver-cd-store.git
git clone https://github.com/QubitPi/screwdriver-cd-queue-service.git
```

### Adding Local Config Files

Create a file called `local.js` in `ui/config` and `local.yaml` in `screwdriver/config` and `local.yaml` in
`queue-service/config` and `store/config` folders.

#### ui/config/local.js

```javascript
let SDAPI_HOSTNAME;
let SDSTORE_HOSTNAME;

SDAPI_HOSTNAME = 'http://sd.screwdriver.cd:9001';
SDSTORE_HOSTNAME = 'http://sd.screwdriver.cd:9002';

module.exports = {
  SDAPI_HOSTNAME,
  SDSTORE_HOSTNAME
};
```

#### screwdriver/config/local.yaml

```
auth:
  jwtPrivateKey: |
    -----BEGIN RSA PRIVATE KEY-----
    *********SOME KEYS HERE********
    -----END RSA PRIVATE KEY-----

  jwtPublicKey: |
    -----BEGIN PUBLIC KEY-----
    ******SOME KEYS HERE******
    -----END PUBLIC KEY-----

httpd:
  # Port to listen on
  port: 9001

  # Host to listen on (set to localhost to only accept connections from this machine)
  host: 0.0.0.0

  # Externally routable URI (usually your load balancer or CNAME)
  # This requires to be a routable IP inside docker for executor, see
  # https://github.com/screwdriver-cd/screwdriver/blob/095eaf03e053991443abcbde91c62cfe06a28cba/lib/server.js#L141
  uri: http://YOUR_IP:9001

ecosystem:
  # Externally routable URL for the User Interface
  ui: http://sd.screwdriver.cd:4200

  # Externally routable URL for the Artifact Store
  store: http://YOUR_IP:9002

  allowCors: ['http://sd.screwdriver.cd', 'http://YOUR_IP:9001']

executor:
  plugin: docker
  docker:
    enabled: true
    options:
      docker:
        socketPath: "/var/run/docker.sock"

scms:
  github:
    plugin: github
    config:
      # github
      oauthClientId: your-oauth-client-id
      oauthClientSecret: your-oauth-client-secret
      secret: a-really-real-secret
      username: sd-buildbot
      email: dev-null@screwdriver.cd
      privateRepo: false

datastore:
  plugin: sequelize
  sequelize:
    # Type of server to talk to
    dialect: sqlite
    # Storage location for sqlite
    storage: ./mw-data/storage.db
```

- (`auth` section) Generate **jwtPrivateKey** and **jwtPublicKey** using

  ```bash
  openssl genrsa -out jwt.pem 2048
  openssl rsa -in jwt.pem -pubout -out jwt.pub
  ```

- (`scms` section) Fill in the Github OAuth **client id** (oauthClientId) and OAuth **client secret**,
  (oauthClientSecret) which can be found in the OAuth application
  [we just created](#creating-a-new-github-oauth-application)
- (`datastore` section) Create a directory called "mw-data" using `mkdir mw-data` under `screwdriver/`
- Replace the `YOUR_IP` above accordingly using `ifconfig`

#### store/config/local.yaml

Similar to the `mw-data` file for the screwdriver repo, we will need to create a folder called "store-data" in our
store repo using `mkdir store-data`

```
auth:
  # A public key for verifying JWTs signed by api.screwdriver.cd
  jwtPublicKey: |
    -----BEGIN PUBLIC KEY-----
    ******SOME KEYS HERE******
    -----END PUBLIC KEY-----

strategy:
  plugin: disk
  disk:
    cachePath: './store-data'
    cleanEvery: 3600000
    partition : 'cache'

httpd:
  port: 9002

ecosystem:
  # Externally routable URL for the User Interface
  ui: http://sd.screwdriver.cd:4200

  # Externally routable URL for the Artifact Store
  api: http://sd.screwdriver.cd:9001

  allowCors: ['http://sd.screwdriver.cd']
```

### Installing Dependencies

Run this command in each of all repos:

```
npm install && npm run start
```

When the UI, Screwdriver API, and Store apps are up and running, we can visit our local screwdriver at
`http://sd.screwdriver.cd:4200`

Setting up Executor Queue and Queue Service
-------------------------------------------

Instead of using single Docker executor, we can use the Redis queue to enable Screwdriver to run more sophisticated
[workflows](https://screwdriver-docs.netlify.app/user-guide/configuration/workflow) such as:
`build_periodically ` and `freezeWindow`.

### Installing Redis

> We use [brew](https://brew.sh/) as a Package Manager for Mac

```bash
brew install redis
```

start Redis with

```bash
brew services start redis
```

Or start if we don't want it run in the background

```bash
redis-server /usr/local/etc/redis.conf
```

Test to see if the Redis server is running:

```bash
redis-cli ping
```

if it replies "PONG", then it's good to go! Note that the location of the Redis configuration file is at

```bash
/usr/local/etc/redis.conf
```

### Getting Source Code

```bash
git clone git@github.com:QubitPi/screwdriver-cd-queue-service.git
```

### Configuring for Local Dev

Create this file for storing local configuration.

```yaml
auth:
  jwtPrivateKey: |
    # paste jwt-qs.pem from previous step
  jwtPublicKey: |
    # paste  jwt-qs.pub from previous step
  # The public key used for verifying the signature of token from SD api
  jwtSDApiPublicKey: |
    # API Public Key generated in earlier step

httpd:
  port: 9003
  host: 0.0.0.0
  uri: http://YOUR_IP:9003

executor:
  plugin: docker
  docker:
    enabled: true
    options:
      docker:
        socketPath: "/var/run/docker.sock"

ecosystem:
  # Externally routable URL for the User Interface
  ui: http://sd.screwdriver.cd:4200
  # Externally routable URL for the API
  api: http://$YOUR_IP:9001
  # Externally routable URL for the Artifact Store
  store: http://$YOUR_IP:9002

queue:
  # Configuration of the redis instance containing resque
  redisConnection:
    host: "127.0.0.1"
    port: 6379
    options:
        password: ''
        tls: false
    database: 0
    prefix: ""
```

Generate **jwtPrivateKey** (jwtPrivateKey) and **jwtPublicKey** (jwtPublicKey) using

```bash
openssl genrsa -out jwt-qs.pem 2048
openssl rsa -in jwt-qs.pem -pubout -out jwt-qs.pub
```

### Modifying screwdriver/config/local.yaml

```yaml
auth:
  jwtQueueServicePublicKey: |
    # paste jwt-qs.pub from previous step

ecosystem:
  # Externally routable URL for the User Interface
  ui: http://sd.screwdriver.cd:4200
  # Externally routable URL for the Artifact Store
  store: http://$YOUR_IP:9002
  # Routable URI of the queue service
  queue: http://$YOUR_IP:9003

executor:
  plugin: queue # <- this step is essential in order to use queue
  queue:
    options:
      # Configuration of the redis instance containing resque
      redisConnection:
        host: "127.0.0.1"
        port: 6379
        options:
          password: ''
          tls: false
        database: 0
        prefix: ""
```

Now, start the Screwdriver backend server and queue service to use Redis queue.

```bash
npm install && npm run start
```

Using Custom Launcher
---------------------

### Building Our Own Launcher Image

```bash
git clone git@github.com:screwdriver-cd/launcher.git
cd launcher
env GOOS=linux GOARCH=arm go build .
docker build . -f Dockerfile.local
# let x be the IMAGE ID. You need to be signed in to your Docker account in Docker app
docker tag X QubitPi/launcher:dev
docker push QubitPi/launcher:dev
```

### Modifying Screwdriver API Repo Config - local.yaml

```yaml
executor:
  plugin: docker
  docker:
    enabled: true
    options:
      launchImage: jithine/launcher
      launchVersion: dev
      docker:
        socketPath: "/var/run/docker.sock"
```
