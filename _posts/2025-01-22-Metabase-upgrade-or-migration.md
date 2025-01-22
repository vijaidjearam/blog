---
layout: post
date: 2025-01-22 11:33:55
title: Metabase upgrade or migrating to new server
category: container
tags: docker container metabase backup migration
---

## Before upgrading the system, always take a backup of the database to prevent data loss.

```bash
docker exec -i postgres /usr/bin/pg_dump -U post-gres-user metabase > /appdata/backup/metabase-postgres-backup-$(date +\%Y-\%m-\%d_\%H-\%M-\%S).sql
```
- This command executes a PostgreSQL dump of the `metabase` database using the `post-gres-user`.
- The backup file is saved with a timestamp in the `/appdata/backup` directory for easy identification.

## Use the stackyml file to deploy the stack. (Not detailed here, assume a separate file for deployment.)
- This step is assumed to be setting up or updating the stack using a YAML configuration.

- Stop the Metabase container to ensure no active connections to the database during the backup restore.


## Copy the backup file to the PostgreSQL container for restoring.

```bash
docker cp metabase-backup.sql postgres:/tmp/metabase-backup.sql
```
- This command copies the backup file `metabase-backup.sql` from the host machine to the PostgreSQL container.
- The file is placed in the `/tmp` directory inside the container.

## Before restoring the backup file, delete the existing 'metabase' database to ensure a fresh restore.
Go into the container cli:

```bash
# Connect to the PostgreSQL container and log in with the specified user to interact with the database.
drobdb -U post-gres-user metabase
# Create a new empty 'metabase' database to restore the backup into.
createdb -U post-gres-user metabase
# Restore the backup file into the newly created 'metabase' database.
psql -U post-gres-user -d metabase < /tmp/metabase-backup.sql
```

- This will restore the backup into the newly created `metabase` database.

## A read-only user is needed for the GLPI database to access data from Metabase.
Go into the container cli:

```bash
# Log into the MariaDB container as the root user to modify the database user and permissions.
mariadb -u root -p
# Create a new user 'metabase' with password 'password' and grant them SELECT privileges on the 'glpi' database.
CREATE USER 'metabase'@'%' IDENTIFIED BY 'password';
GRANT SELECT ON glpi.* TO 'metabase'@'%';
FLUSH PRIVILEGES;
```
- This creates a `metabase` user and gives it read-only (`SELECT`) access to the `glpi` database.

```bash
# To check the privileges of the 'metabase' user on the 'glpi' database, run the following command:
mariadb -u root -p
SELECT user, host, db, select_priv, insert_priv, update_priv, delete_priv, create_priv, drop_priv, grant_priv
FROM mysql.db;
```

- This query will show the privileges of the `metabase` user, ensuring that `SELECT` permission is granted.

```bash
# If you forgot the password for the user, you can delete and recreate the user with the following commands.
mariadb -u root -p
DROP USER 'username'@'hostname';
FLUSH PRIVILEGES;
```

- This command deletes the specified user, allowing for a recreation with a new password if necessary.
