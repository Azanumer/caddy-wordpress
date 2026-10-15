# Caddy for WordPress

Production-ready [Caddy v2](https://caddyserver.com) configs for WordPress with
automatic HTTPS, pretty permalinks, static-asset caching and sensitive-file blocking.

## Files

| File | What it is |
|---|---|
| `Caddyfile` | Site block for a single VPS: PHP-FPM via unix socket, gzip+zstd, security headers |
| `hardening-snippet.caddy` | Optional import: rate-limit `wp-login.php`, block scanner user-agents |
| `docker/docker-compose.yml` | Full stack: Caddy + `wordpress:php8.2-fpm` + MariaDB, only Caddy on 80/443 |
| `docker/Caddyfile.docker` | Caddyfile for the Docker stack (`php_fastcgi wordpress:9000`) |

## Quick start — VPS

1. Install Caddy and PHP-FPM, extract WordPress to `/var/www/wordpress`.
2. Copy `Caddyfile`, replace `example.com` and the `php_fastcgi` socket path with yours
   (`/run/php/php8.2-fpm.sock` is shown; check `ls /run/php/`).
3. `caddy fmt --overwrite /etc/caddy/Caddyfile && caddy validate --config /etc/caddy/Caddyfile`
4. `systemctl reload caddy` — HTTPS is issued automatically on first request.

## Quick start — Docker

```bash
cd docker
echo "DB_PASSWORD=$(openssl rand -hex 16)" > .env
# edit Caddyfile.docker: replace example.com and the email
docker compose up -d
```

## Notes

- The `@blocked` matcher returns 404 for `wp-config.php`, `.git`/`.env`/`.sql` paths and friends.
- Versioned theme/plugin assets get a 1-year immutable cache; admin, login, cron and REST are `no-store`.
- If you use the official `wordpress:apache` image instead of `-fpm`, replace `php_fastcgi` with `reverse_proxy`.

MIT licensed.
