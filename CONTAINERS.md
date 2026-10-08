# Running with Podman

The local development stack runs the SvelteKit client, Spring Boot server, and a file-backed H2 database with Podman Compose. The client and server source directories are mounted into their containers for development. Client changes are picked up by Vite polling; server changes are compiled by the `server-compiler` service and trigger a Spring DevTools restart. The database and dependency caches use named volumes.

## Start the app

Install Podman and a Compose provider (`podman-compose` or `docker-compose`), then run from the repository root:

```sh
podman compose up --build
```

Open <http://localhost:5173>. The API is available at <http://localhost:8080/api> and its OpenAPI docs at <http://localhost:8080/api/v1/public/docs>.

If port `8080` is already in use, choose another host port for both the published API and the client configuration:

```sh
SERVER_HOST_PORT=8081 podman compose up --build -d
```

The API is then available at <http://localhost:8081/api>.

The backend uses the existing `dev` profile and applies the project’s sample data the first time the database is created. Liquibase is configured not to drop the schema on subsequent starts, and H2 stores its files in the `h2-data` named volume.

## Stop and reset

Stop the containers and keep the database:

```sh
podman compose down
```

Remove the containers and database data to start fresh (sample data will be seeded again):

```sh
podman compose down --volumes
```

Follow service logs with `podman compose logs -f server-compiler server client`.
