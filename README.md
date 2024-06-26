# Development Setup with Frappe!

## Introduction

This is a test project for learning frappe and bench. This repo has the config for running a development setup using docker-compose. This is based on the steps found at [frappe_docker docs](https://github.com/frappe/frappe_docker/blob/main/docs/development.md)

## Development

### Start dev containers on Linux

Clone this repo and run the following commands

```bash
export UID
GID=$(id -g) docker-compose up -d
```

### Start dev containers on macOS

Clone this repo and run the following commands

```bash
UID=$(id -u) GID=$(id -g) docker-compose up
```

### Connect to the frappe dev container

In order  to use the bench cli, connect to the frappe-dev container:

```bash
docker exec -it frappe-dev-frappe-1 /bin/bash
```

This opens up bash shell as user frappe if the user id is matched. Otherwise, run `su frappe` to change user to frappe before continuing development.

NOTE: If the user id on host is not the same as user id of frappe in the container, there could be issues with file ownership and permissions. Please refer frappe_docker docs for more information.

## Creating a bench along with a new site

This needs to be done only once when setting up the development environment. To start an env that has been already setup jump to next section.

```bash
# Create bench
bench init --skip-redis-config-generation frappe-bench
cd frappe-bench

# Setup hosts
bench set-config -g db_host mariadb
bench set-config -g redis_cache redis://redis-cache:6379
bench set-config -g redis_queue redis://redis-queue:6379
bench set-config -g redis_socketio redis://redis-queue:6379

# Create a new site
bench new-site --no-mariadb-socket development.localhost
# bench new-site --mariadb-root-password 123 --admin-password admin --no-mariadb-socket development.localhost

# Set developer mode
bench --site development.localhost set-config developer_mode 1
bench --site development.localhost clear-cache

# Start bench
bench start # without debugging
bench --site development.localhost console # with debugging
```

After this step, the locally created site can be accessed via http://development.localhost:8000

## Start up bench

```bash
cd frappe-bench

# Start bench
bench start # without debugging
bench --site development.localhost console # with debugging
```

After this step, the locally created site can be accessed at [development.local:8000](http://development.localhost:8000)

## Shutdown the bench

When you are done for the day, stop the webserver by pressing `Ctrl+C` at the frappe dev container shell. This is optional.

Stop the dev containers, using the steps below.

### On Linux

```bash
export UID
GID=$(id -g) docker-compose down
```

### On macOS

```bash
UID=$(id -u) GID=$(id -g) docker-compose down
```

## Next steps

Once the development setup is ready, this can be used for trying out or developing new apps. Each app is typically maintained in its own git repo. Consult frappe framework docs for [creating an app](https://frappeframework.com/docs/user/en/tutorial/create-an-app) or [adding an existing app](https://frappeframework.com/docs/user/en/bench/bench-commands#add-apps).
