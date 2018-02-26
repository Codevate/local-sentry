### Local Sentry

Bootstrap a local Sentry server to test project integrations and make sure your errors are getting logged correctly. This is **not intended for production**, instead you should use the official [on-premise setup](https://github.com/getsentry/onpremise).

## Getting started

Start by copying the example environment variables file:

```bash
cp .env.example .env
```

Generate a key and assign `SENTRY_SECRET_KEY` in `.env` to it:

```bash
docker-compose run --rm web config generate-secret-key
```

Build the database and use the interactive prompts to create a super-user account:

```bash
docker-compose run --rm web upgrade
```

Lift all services in the background:

```bash
docker-compose up -d
```

Once the containers are running, go to http://localhost:9090 and set this as root URL **with no trailing slash**. Fill in the other fields and finish the setup.

If there are any issues you can check the logs via:

```bash
docker-compose logs -f
```

## Networking with Docker

If your project is running in Docker, you'll have to network it with the Sentry web container:

```bash
docker network connect --alias sentry your_network sentry_web_1
```

Then adjust any generated DSNs so that `localhost:9090` is replaced with `sentry:9000`, e.g.

```
http://xxxx:xxxx@sentry:9000/2
```

## Configuring Sentry

Because the [sentry:onbuild](https://hub.docker.com/_/sentry/) image is used, you can edit the files in `/docker/sentry` to customise your instance & install plugins.

## Troubleshooting

If you exit the upgrade while the migrations are running, the internal Sentry project won't be created and you won't be prompted to create a user. This process can also stall due to Docker not having enough RAM available. Running `upgrade` on its own won't fix this, you'll have to destroy everything and try again:

```bash
 docker-compose down -v
 docker-compose run --rm web upgrade
```


