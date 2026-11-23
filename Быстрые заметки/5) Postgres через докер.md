#### Постгрес:
---
```bash
docker run --name postgres -e POSTGRES_PASSWORD=postgres -p 55432:5432 -v postgres_data:/var/lib/postgresql -d postgres:18.6
```
#### PgAdmin
---
```bash
docker run --name pgadmin -p 55433:80 -e "PGADMIN_DEFAULT_EMAIL=pgadmin@example.com" -e "PGADMIN_DEFAULT_PASSWORD=password" -e "PGADMIN_CONFIG_MASTER_PASSWORD_REQUIRED=False" -e "PGADMIN_CONFIG_SERVER_MODE=False" -v pgadmin_data:/var/lib/pgadmin -d dpage/pgadmin4
```
