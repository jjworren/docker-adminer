# https://github.com/jjworren/docker-adminer

## Forked from: [Tim Düsterhus (of the Docker Community)](https://github.com/TimWolla/docker-adminer)

# Supported tags and respective `Dockerfile` links

-	[`4.8.1`, `4`, `latest`](https://github.com/TimWolla/docker-adminer/blob/c9c54b18f79a66409a3153a94f629ea68f08647c/4/Dockerfile)

# Quick reference (cont.)

-	**Where to file issues**:  
	[https://github.com/TimWolla/docker-adminer/issues](https://github.com/TimWolla/docker-adminer/issues?q=)

-	**Supported architectures**: ([more info](https://github.com/docker-library/official-images#architectures-other-than-amd64))  
	[`amd64`](https://hub.docker.com/r/amd64/adminer/), [`arm32v5`](https://hub.docker.com/r/arm32v5/adminer/), [`arm32v7`](https://hub.docker.com/r/arm32v7/adminer/), [`arm64v8`](https://hub.docker.com/r/arm64v8/adminer/), [`i386`](https://hub.docker.com/r/i386/adminer/), [`mips64le`](https://hub.docker.com/r/mips64le/adminer/), [`ppc64le`](https://hub.docker.com/r/ppc64le/adminer/), [`s390x`](https://hub.docker.com/r/s390x/adminer/)

-	**Published image artifact details**:  
	[repo-info repo's `repos/adminer/` directory](https://github.com/docker-library/repo-info/blob/master/repos/adminer) ([history](https://github.com/docker-library/repo-info/commits/master/repos/adminer))  
	(image metadata, transfer size, etc)

-	**Image updates**:  
	[official-images repo's `library/adminer` label](https://github.com/docker-library/official-images/issues?q=label%3Alibrary%2Fadminer)  
	[official-images repo's `library/adminer` file](https://github.com/docker-library/official-images/blob/master/library/adminer) ([history](https://github.com/docker-library/official-images/commits/master/library/adminer))

-	**Source of this description**:  
	[docs repo's `adminer/` directory](https://github.com/docker-library/docs/tree/master/adminer) ([history](https://github.com/docker-library/docs/commits/master/adminer))

# Adminer

## What is Adminer?

Adminer (formerly phpMinAdmin) is a full-featured database management tool written in PHP. Conversely to phpMyAdmin, it consist of a single file ready to deploy to the target server. Adminer is available for MySQL, PostgreSQL, SQLite, MS SQL, Oracle, Firebird, SimpleDB, Elasticsearch and MongoDB.

> [adminer.org](https://www.adminer.org)

![logo](https://raw.githubusercontent.com/docker-library/docs/95569c9119afe7b11a233105d398f99d93d2fcce/adminer/logo.png)

## How to use this image

### Standalone

```console
$ docker run --link some_database:db -p 8080:8080 adminer
```

Then you can hit `http://localhost:8080` or `http://host-ip:8080` in your browser.

### FastCGI

If you are already running a FastCGI capable web server you might prefer running Adminer via FastCGI:

```console
$ docker run --link some_database:db -p 9000:9000 adminer:fastcgi
```

Then point your web server to port 9000 of the container.

Note: This exposes the FastCGI socket to the Internet. Make sure to add proper firewall rules or use a private Docker network instead to prevent a direct access.

### ... via [`docker-compose`](https://github.com/docker/compose) or [`docker stack deploy`](https://docs.docker.com/engine/reference/commandline/stack_deploy/)

Example `docker-compose.yml` for `adminer`:

```yaml
# Use root/example as user/password credentials

version: '3.1'

services:

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080

  db:
    image: mysql:5.6
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
```

[![Try in PWD](https://github.com/play-with-docker/stacks/raw/cff22438cb4195ace27f9b15784bbb497047afa7/assets/images/button.png)](http://play-with-docker.com?stack=https://raw.githubusercontent.com/docker-library/docs/9efeec18b6b2ed232cf0fbd3914b6211e16e242c/adminer/stack.yml)

Run `docker stack deploy -c stack.yml adminer` (or `docker-compose -f stack.yml up`), wait for it to initialize completely, and visit `http://swarm-ip:8080`, `http://localhost:8080`, or `http://host-ip:8080` (as appropriate).

### Loading plugins

This image bundles all official Adminer plugins. You can find the list of plugins on GitHub: https://github.com/vrana/adminer/tree/master/plugins.

To load plugins you can pass a list of filenames in `ADMINER_PLUGINS`:

```console
$ docker run --link some_database:db -p 8080:8080 -e ADMINER_PLUGINS='tables-filter tinymce' adminer
```

If a plugin *requires* parameters to work correctly instead of adding the plugin to `ADMINER_PLUGINS`, you need to add a custom file to the container:

```console
$ docker run --link some_database:db -p 8080:8080 -e ADMINER_PLUGINS='login-servers' adminer
Unable to load plugin file "login-servers", because it has required parameters: servers
Create a file "/var/www/html/plugins-enabled/login-servers.php" with the following contents to load the plugin:

<?php
require_once('plugins/login-servers.php');

/** Set supported servers
    * @param array array($domain) or array($domain => $description) or array($category => array())
    * @param string
    */
return new AdminerLoginServers(
    $servers = ???,
    $driver = 'server'
);
```

To load a custom plugin you can add PHP scripts that return the instance of the plugin object to `/var/www/html/plugins-enabled/`.

### Choosing a design

The image bundles all the designs that are available in the source package of adminer. You can find the list of designs on GitHub: https://github.com/vrana/adminer/tree/master/designs.

To use a bundled design you can pass its name in `ADMINER_DESIGN`:

```console
$ docker run --link some_database:db -p 8080:8080 -e ADMINER_DESIGN='nette' adminer
```

To use a custom design you can add a file called `/var/www/html/adminer.css`.

### Usage with external server

You can specify the default host with the `ADMINER_DEFAULT_SERVER` environment variable. This is useful if you are connecting to an external server or a docker container named something other than the default `db`.

```console
docker run -p 8080:8080 -e ADMINER_DEFAULT_SERVER=mysql adminer
```

## Supported Drivers

While Adminer supports a wide range of database drivers this image only supports the following out of the box:

-	MySQL
-	PostgreSQL
-	SQLite
-	SimpleDB
-	Elasticsearch

To add support for the other drivers you will need to install the following PHP extensions on top of this image:

-	`pdo_dblib` (MS SQL)
-	`oci8` (Oracle)
-	`interbase` (Firebird)
-	`mongodb` (MongoDB)

# License

View [license information](https://github.com/vrana/adminer/blob/master/readme.txt) for the software contained in this image.

As with all Docker images, these likely also contain other software which may be under other licenses (such as Bash, etc from the base distribution, along with any direct or indirect dependencies of the primary software being contained).

Some additional license information which was able to be auto-detected might be found in [the `repo-info` repository's `adminer/` directory](https://github.com/docker-library/repo-info/tree/master/repos/adminer).

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.