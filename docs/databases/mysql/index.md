# MYSQL Cheatsheet

## I. Docker

### 1.1. Configuration

> docker-compose.yml

```yml
version: "3.4"

services:
  mysql:
    image: mysql:8.0.33-oracle
    container_name: nextshop_mysql
    shm_size: 1g
    restart: unless-stopped
    volumes:
      - nextshop_db:/var/lib/mysql
      - ./dumps:/var/dumps
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: nextshop_mysql
    ports:
      - 3306:3306
    networks:
      - nextshop
networks:
  nextshop:
volumes:
  nextshop_db: {}
```

### 1.2. Up and down

```sh
# Up and down
docker-compose -p "nextshop" up -d
docker exec -it nextshop_mysql sh
```

### 1.3. Backup

```sh
mysqldump --user=root --password=123456 nextshop_mysql > "/var/dumps/nextshop_mysql_dump_$(date +%Y%m%d%H%M%S).sql"
```

### 1.4. Restore

```sh
mysql -u root -p 123456 nextshop_mysql < /var/dumps/nextshop_mysql_dump_20240528103452.sql
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
mysqldump --user=root --password=123456 nextshop_mysql > "/var/dumps/nextshop_mysql_dump_$(date +%Y%m%d%H%M%S).sql"
# exit from server
exit
# ssh ftp to download backup file to local
sftp dbadmin@ip-address:/var/dumps/nextshop_mysql_dump_20240528103452.sql $HOME
cd $HOME
# extract
mysql -u root -p 123456 nextshop_mysql < nextshop_mysql_dump_20240528103452.sql

```

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
