## pyk's home labs

Create new JWT secret:

```sh
openssl rand -hex 32 > jwtsecret
```

Run all services:

```sh
docker compose up -d
```

Check logs:

```sh
docker compose logs -f [service-name]
```
