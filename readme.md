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

The cluster comes with a [GraphQL Playground](https://github.com/graphcool/graphql-playground) at http://localhost:4466/cluster – Here you can obtain cluster information like the deployed projects, etc. For example:

```graphql
{
  listProjects {
    name
  }
}
```

## Configuration

You can find two separate `env` files in this repository: `.mysql.env` and `.prisma.env`. Each of them is attached to the respective container via `env_file` in the `docker-compose.yml`.

## Connecting to the cluster via `Prisma CLI`

The central place for configuring your Prisma CLI is the file `~/.prisma/config.yml`. You can add your new cluster to this file by executing `prisma cluster add`:

```
❯ prisma cluster add
? Please provide the cluster endpoint http://localhost:4466
? Please provide the cluster secret
? Please provide a name for your cluster myprismacluster
```

**Important:** Leave `cluster secret` empty. This is because we haven't configured the `CLUSTER_PUBLIC_KEY` in `.prisma.env`. Please note that this is only for development purposes. You have to generate a key pair for securing your cluster if you want to create a production environment. You can find more information about how to secure your cluster in the [docs](<https://www.prismagraphql.com/docs/tutorials/cluster-deployment/digital-ocean-(docker-machine)-texoo9aemu#7.-enable-cluster-authentication>). Your `cluster secret` would then the generated `private` key, whereas the public key has to be placed as `CLUSTER_PUBLIC_KEY` in `.prisma.env`.

If you used one of the boilerplates, like [typescript-graphql-server](https://github.com/graphql-boilerplates/typescript-graphql-server), you have to configure the `.env` file in there as follows:

```
...
PRISMA_ENDPOINT="http://localhost:4466/<your-project-name>/dev"
PRISMA_CLUSTER="myprismacluster"
...
```

Afterwards, you can deploy the schema via `prisma deploy`.

## License

MIT © [André König](https://andrekoenig.de)
