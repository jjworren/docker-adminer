# https://github.com/jjworren/docker-adminer

## Forked from: [Tim Düsterhus (of the Docker Community)](https://github.com/TimWolla/docker-adminer)

# Supported tags and respective `Dockerfile` links

-	[`v4.8.1`, `latest`](https://github.com/jjworren/docker-adminer/tree/v4.8.1/Dockerfile)

# Adminer

## What is Adminer?

Adminer (formerly phpMinAdmin) is a full-featured database management tool written in PHP. Conversely to phpMyAdmin, it consist of a single file ready to deploy to the target server. Adminer is available for MySQL, PostgreSQL, SQLite, MS SQL, Oracle, Firebird, SimpleDB, Elasticsearch and MongoDB.

> [adminer.org](https://www.adminer.org)

![logo](https://raw.githubusercontent.com/docker-library/docs/95569c9119afe7b11a233105d398f99d93d2fcce/adminer/logo.png)

## How to use this image

### Standalone

```console
$ docker run --link some_database:db -p 8080:8080 jjworren/adminer
```

Then you can hit `http://localhost:8080` or `http://host-ip:8080` in your browser.

### ... via [`docker-compose`](https://github.com/docker/compose) or [`docker stack deploy`](https://docs.docker.com/engine/reference/commandline/stack_deploy/)

Example `docker-compose.yml` for `adminer`:

```yaml
# Use root/example as user/password credentials

version: '3.1'

services:

  adminer:
    image: jjworren/adminer
    restart: always
    ports:
      - 8080:8080

  db:
    image: mysql:5.6
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example
```

Run `docker stack deploy -c stack.yml adminer` (or `docker-compose -f stack.yml up`), wait for it to initialize completely, and visit `http://swarm-ip:8080`, `http://localhost:8080`, or `http://host-ip:8080` (as appropriate).

### Loading plugins

This image bundles all official Adminer plugins. You can find the list of plugins on GitHub: https://github.com/vrana/adminer/tree/master/plugins.

To load plugins you can pass a list of filenames in `ADMINER_PLUGINS`:

```console
$ docker run --link some_database:db -p 8080:8080 -e ADMINER_PLUGINS='tables-filter tinymce' jjworren/adminer
```

If a plugin *requires* parameters to work correctly instead of adding the plugin to `ADMINER_PLUGINS`, you need to add a custom file to the container:

```console
$ docker run --link some_database:db -p 8080:8080 -e ADMINER_PLUGINS='login-servers' jjworren/adminer
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

### Env Variables

Handy for lazy usage in docker compose for development purposes

| Var | Description | example |
| - | - | - |
| ADMINER_DEFAULT_SERVER | Default server prefilled in login screen | postgres |
| ADMINER_DEFAULT_DRIVER | Default driver preselected in login screen | psql |
| ADMINER_DEFAULT_USERNAME | Default username preffiled in login screen | pguser |
| ADMINER_DEFAULT_PASSWORD | Default password preffiled in login screen | pqpassword |
| ADMINER_DEFAULT_DB | Default db name prefilled in login screen | postgres |

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

As for any pre-built image usage, it is the image user's responsibility to ensure that any use of this image complies with any relevant licenses for all software contained within.