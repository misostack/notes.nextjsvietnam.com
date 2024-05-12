# PostgreSQL Cheatsheet

## I. Docker

### 1.1. Configuration

> docker-compose.yml

```yml
version: "3"
services:
  # redis:
  #   image: redis
  postgres:
    container_name: postgres_container
    image: postgres:14.8-alpine
    shm_size: 1g
    restart: unless-stopped
    ports:
      - "5432:5432"
    environment:
      POSTGRES_PASSWORD: pybase_dev
      POSTGRES_USER: YOUR_PASSWORD
    volumes:
      - ./.docker/postgres_init.sql:/docker-entrypoint-initdb.d/postgres_init.sql
      - ./backups:/home/backups
      - ./restores:/home/restores
      - pybase_dev_data:/var/lib/postgresql/data
  pgadmin:
    container_name: pgadmin_container
    image: dpage/pgadmin4:8.0
    shm_size: 1g
    restart: unless-stopped
    depends_on:
      - postgres
    ports:
      - "5050:80"
    environment:
      PGADMIN_DEFAULT_EMAIL: techlead@sonnm.com
      PGADMIN_DEFAULT_PASSWORD: YOUR_PASSWORD
      PGADMIN_CONFIG_SERVER_MODE: "False"
    volumes:
      - pgadmin_data:/var/lib/pgadmin
volumes:
  pybase_dev_data:
  pgadmin_data:
```

> .docker/postgres_init.sql

```sql
DO
$do$
DECLARE user_admin CONSTANT varchar := 'pybase_dev';
DECLARE user_admin_pw CONSTANT varchar := 'YOUR_PASSWORD';

BEGIN

      IF NOT EXISTS (
          SELECT FROM pg_catalog.pg_user p
          WHERE usename = user_admin) THEN
           EXECUTE 'CREATE USER '|| user_admin ||' WITH SUPERUSER PASSWORD '|| quote_literal(user_admin_pw);
      END IF;
END $do$;

CREATE DATABASE pybase_dev;
GRANT ALL PRIVILEGES ON DATABASE pybase_dev TO pybase_dev;

\connect pybase_dev
CREATE SCHEMA IF NOT EXISTS dbo AUTHORIZATION  pybase_dev;
\q
```

### 1.2. Up and down

```sh
# Up and down
docker-compose -f docker-compose.dev.yml up -d
docker-compose -f docker-compose.dev.yml down
```

### 1.3. Backup

```sh
docker exec -it postgres_container sh -c 'pg_dump -v --clean --username=pybase_dev --dbname=pybase_dev > /home/backups/pybase_dev_14052023.sql'
docker exec -it postgres_container sh -c 'pg_dump --format=tar --clean --username=pybase_dev --dbname=pybase_dev > /home/backups/pybase_dev_14052023.tar'
docker exec -it postgres_container sh -c 'pg_dump -vc --username=pybase_dev --dbname=pybase_dev | gzip > /home/backups/pybase_dev_14052023.sql.gz'
```

### 1.4. Restore

```sh
# restore with dump format
docker exec -it postgres_container sh -c 'pg_restore -vc --username=pybase_dev --dbname=pybase_dev < /home/backups/pybase_dev_14052023.tar'
# restore with sql format
# postgres://pybase_dev:pybase1337@127.0.0.1:5432/pybase_dev
docker exec -it postgres_container sh -c 'psql -vcC postgres://pybase_dev:pybase1337@127.0.0.1:5432/pybase_dev < /home/backups/pybase_dev_14052023.sql -v ON_ERROR_STOP=1'
# restore with gzip format
docker exec -it postgres_container sh -c 'zcat /home/backups/pybase_dev_14052023.sql.gz | psql -vc --username=pybase_dev --dbname=pybase_dev -v ON_ERROR_STOP=1'
```

### 1.5. Download and Upload

```sh
sftp -r username@hostname:/home/backups/* /backups/
```

```sh
sftp -r /backups/* username@hostname:/home/backups/
```

## Administration

### Manage users

## Backup and restore postgresql database

```sh
ssh dbadmin@ip-address
sudo -i -u postgres
# dump database
pg_dump -d dbname > $HOME/db-backup-filename.sql
# compress sql file
tar -czvf db-backup-filename.tar.gz db-backup-filename.sql
# exit postgres user
exit
sudo cp /var/lib/postgresql/db-backup-filename.tar.gz $HOME
# exit from server
exit
# ssh ftp to download backup file to local
sftp dbadmin@ip-address:/home/dbadmin/db-backup-filename.tar.gz $HOME
cd $HOME
# extract
tar -xvf db-backup-filename.tar.gz -C

```

**Usually, when we export database from production, the db owner is another user. We have several common ways to by pass this**

1. The 1st way : Create new local user which is same as db owner
2. The 2nd way : change the db owner in sql script

Here is 2 steps

## Design Database

### Naming rules

```ts
Primary Key - PK_TableName_ColumnName(s)
ForeignKey - FK_TableName_ColumnName_ReferenceTable_ReferenceColumn
Unique - UNQ_TableName_ColumnName
Check - CHK_Table_Name_Condition
Clustered Index - IDX_Clust_TableName_Columns
NonClustered Index - IDX_NC_TableName_Columns
```
