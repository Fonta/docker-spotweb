[![](https://img.shields.io/docker/pulls/fontaaaaa/spotweb.svg)](https://hub.docker.com/r/fontaaaaa/spotweb/)

# Docker Spotweb image
Docker image running Alpine and Spotweb

# Image builds
The image with the `dev-latest` tag of the docker image is built using the develop branch of [Spotweb](https://github.com/spotweb/spotweb).
The image with the `latest` tag is built using the master branch of Spotweb.

Image builds have been scheduled on a daily basis, but will only produce a new imaghe when changes are detected in the branch.

## Forked from
This repository has been forked from [Erik de Vries](https://github.com/edv/docker-spotweb) and adjusted to my needs have it build on a daily basis.

## Getting started
The easiest way to get Spotweb up and running is using the included `docker-compose.yml` file. This will run Spotweb (image published to Docker Hub) together with Postgres.

```bash
docker compose up -d
```

### Database configuration
Spotweb requires a database to work. Out of the box this project supports MySQL, PostgreSQL and SQLite. Provide one or more of the following environment variables to configure the database:

- **DB_ENGINE** one of:
  - `pdo_mysql` (MySQL, default)
  - `pdo_pgsql` (PostgreSQL)
  - `pdo_sqlite` (SQLite)
- **DB_HOST** (default = `mysql`)
- **DB_PORT** (default = `3306`)
- **DB_NAME** (default = `spotweb`)
- **DB_USER** (default = `spotweb`)
- **DB_PASS** (default = `spotweb`)

#### Examples
Example docker compose files are included in the `examples` directory.

#### External database
To connect to an external database configure the `DB_HOST`. Make sure the database server allows external access and correct credentials and/or port settings are configured.

```bash
docker run -p 8085:80 \
  --name spotweb -d \
  -e DB_HOST=mysql.hostname \
  fontaaaaa/spotweb:develop
```

## Configure Spotweb
- Visit `http://localhost:8085`
- Login with username `admin` and default password `spotweb`
- Configure usenet server, spot retention, etc. and wait for spots to appear (retrieval script by default runs once every 5 minutes, see below how to change this update interval)

## Change Spotweb update interval
- By default Spotweb will update every 5 minutes
- Change the `CRON_INTERVAL` environment variable to any valid cronjob expression (see e.g. https://cron.help/ for more information, default 5 minute interval is configured in the docker-compose.yml file as an example)
- Restart the Spotweb Docker container (during start-up it will display the current configured update interval)

## Store cache outside container
- By default Spotweb store cache (like images) inside of the Docker container (in `/app/cache`)
- This results in the cache being removed when the container is recreated (e.g. when a new Docker image is pulled)
- To retain this cache you can mount a volume to `/app/cache` see the commented lines in the included Docker Compose files on how to do this

## Change timezone
- Change the `TZ` environment variable to any valid timezone (e.g. Europe/Amsterdam or Europe/Lisbon)
- Restart the Spotweb Docker container

## Redirect access log
By default, access logs are sent to stdout. In case you want to change it:
* Set the `ACCESS_LOG` environment variable to the location of your liking (e.g. `/var/log/nginx/access.log`)
* Restart the Spotweb Docker container

## Use Spotweb as newznab provider (indexer)
If you want to use Spotweb with for example Sonarr or Radarr (or any tool that is compatible with newznab indexers), create a new (non admin) user in Spotweb and use the API key associated with this new user.
Next step is to set-up a custom newznab indexer in Sonarr or Radarr and point it to the Spotweb url with the API key from the newly created user.

## All environment variables
- **DB_ENGINE**, one of:
  - `pdo_mysql` (MySQL, default)
  - `pdo_pgsql` (PostgreSQL)
  - `pdo_sqlite` (SQLite)
- **DB_HOST** (default = `mysql`)
- **DB_PORT** (default = `3306`)
- **DB_NAME** (default = `spotweb`)
- **DB_USER** (default = `spotweb`)
- **DB_PASS** (default = `spotweb`)
- **TZ** (default = `Europe/Amsterdam`)
- **CRON_INTERVAL** (default = `*/5 * * * *`)
- **ACCESS_LOG** (default = `/dev/stdout`)

## Using `ownsettings.php`
You can override Spotweb settings by using a custom `ownsettings.php` file. In most cases there is no need to use this feature, so only use this when you know what you are doing!
The example below will mount `/external/ownsettings.php` to `/app/ownsettings.php` inside the container. Spotweb will see the ownsettings file and load it automatically.

```
volumes:
- /external/ownsettings.php:/app/ownsettings.php
```

## Additional information
- Spotweb is by default configured as an open system after running docker compose up, so everyone who can access the site can register an account (keep this in mind, and also make sure to change the admin password if you plan to expose Spotweb to the outside world!). Read the documentation if you want to change Spotweb into a closed system.
- See the [official Spotweb Wiki](https://github.com/spotweb/spotweb/wiki) for any questions regarding Spotweb
