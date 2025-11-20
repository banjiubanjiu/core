# Docker quick start

This compose setup starts two services:

- **app**: Apache + PHP 8.1 with all required extensions. Composer deps are installed during the image build.
- **db**: MySQL 8.0 with the schema seeded from `../gibbon.sql`.

## Prerequisites

- Docker Engine + Docker Compose V2
- Host ports `8080` (HTTP) and `3306` (MySQL) available

## Start

```bash
docker compose -f docker/docker-compose.yaml up -d --build
```

Open `http://localhost:8080/` (it redirects to `./installer/install.php`) and follow the installer.

Installer database values:

- Host: `db`
- DB name: `gibbon`
- DB user: `gibbon`
- DB password: `gibbon`

The installer writes `config.php` to the bind mount and initializes the DB. For demo data instead of an empty schema, swap the init SQL in `docker-compose.yaml` to `../gibbon_demo.sql`.

## Persistent data

- `docker/runtime/config/config.php` -> `/var/www/html/config.php`
- `docker/runtime/mysql` -> `/var/lib/mysql`
- `uploads` -> `/var/www/html/uploads`
- `resources/assets/compiled` -> `/var/www/html/resources/assets/compiled`
- `docker/runtime/apache-logs` -> `/var/log/apache2`

## Useful commands

- Stop: `docker compose -f docker/docker-compose.yaml down`
- Logs: `docker compose -f docker/docker-compose.yaml logs -f app`
- Rebuild app image: `docker compose -f docker/docker-compose.yaml build --no-cache app`

## Fresh install checklist

If you need to reinstall or run the installer from scratch, do these steps in order:

1) Clear config and restart app (re-enable installer)
```bash
cd docker
Set-Content .\runtime\config\config.php ""
docker compose -f docker-compose.yaml restart app
```
2) Reset database to empty
```bash
docker compose -f docker-compose.yaml exec db mysql -uroot -proot -e "DROP DATABASE IF EXISTS gibbon; CREATE DATABASE gibbon CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci; GRANT ALL PRIVILEGES ON gibbon.* TO 'gibbon'@'%'; FLUSH PRIVILEGES;"
```
3) Open the installer
   - Visit `http://localhost:8080/` (auto-redirect to installer)
   - Database: host `db`, port `3306`, name/user/password `gibbon`
   - Set admin username/password
   - Choose Demo Data = yes (for quick trial) or no (for real use)
4) After install finishes, `config.php` is written to `docker/runtime/config/config.php` and DB data lives in `docker/runtime/mysql/`.
