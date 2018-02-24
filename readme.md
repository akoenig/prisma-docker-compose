# Prisma: Docker Compose setup

This repository demonstrates how to start a Prisma cluster (MySQL + Prisma) via Docker Compose.

## Usage

```
git clone git@github.com:akoenig/prisma-docker-compose.git && cd prisma-docker-compose

docker-compose up
```

**Important:** You might see the following exception from the Prisma container on the first run:

```
Exception in thread "main" java.sql.SQLTransientConnectionException: internalRoot - Connection is not available, request timed out after 5001ms.
prisma      | 	at com.zaxxer.hikari.pool.HikariPool.createTimeoutException(HikariPool.java:548)
prisma      | 	at com.zaxxer.hikari.pool.HikariPool.getConnection(HikariPool.java:186)
prisma      | 	at com.zaxxer.hikari.pool.HikariPool.getConnection(HikariPool.java:145)
prisma      | 	at com.zaxxer.hikari.HikariDataSource.getConnection(HikariDataSource.java:83)
...
```

This happens when your MySQL instance takes longer to boot than the Prisma server. In this case you only have to wait for a container rescheduling by the Docker engine. The Prisma container will be started after a while and you should see this output then:

```
prisma      | Obtaining exclusive agent lock...
prisma      | Obtaining exclusive agent lock... Successful.
prisma      | Deployment worker initialization complete.
prisma      | Initializing workers...
prisma      | Successfully started 1 workers.
prisma      | Server running on :4466
prisma      | Version is up to date.
```

The cluster comes with a [GraphQL Playground](https://github.com/graphcool/graphql-playground) at http://localhost:4466/cluster – Here you can obtain cluster information like the deployed projects, etc.

## Using `.env` files

If you don't like the environment variables defined in your `docker-compose.yml`, you can easily move them to an `.env` file, which you can reference within your `docker-compose.yml` via `env_file` then. So instead of ...

```
environments:
  - MYSQL_ROOT_PASSWORD: foo
  ...
```

... you define

```
env_file: path/to/your/.env
```

... in your `docker-compose.yml` and place the variables in `.env` as:

```
MYSQL_ROOT_PASSWORD=foo
...
```

## License

MIT © [André König](https://andrekoenig.de)
