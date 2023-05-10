---
layout: post
date: 2023-05-10 12:09:00
title: How_to_Reset_MySQL_Master-Slave_Replication
category: Glpi
tags: glpi inventory 
---
# How to Reset ( Re-Sync ) MySQL Master-Slave Replication
Source: [https://tecadmin.net/reset-re-sync-mysql-master-slave-replication/](https://tecadmin.net/reset-re-sync-mysql-master-slave-replication/)

This article will guide you to how to reset MySQL replication and it will start again from scratch.

Warning: After using this tutorial, All of your bin-log files will be deleted, So if you want, you may take a backup of bin-log files first and then follow the instructions.

## At Slave Server:

At first we need to stop slave on slave server. Login to the MySQL server and execute the following command.

```
mysql> STOP SLAVE;
```
## At Master Server:

After stopping slave go to master server and reset the master state using following command.

```
mysql> RESET MASTER;
mysql> FLUSH TABLES WITH READ LOCK;

```

Take a dump of database is being replicated using following command.

```
mariadb -u root -p mydb > mydb-dump.sql

```
After taking backup unlock the tables at master server.

```
mysql> UNLOCK TABLES;
```

## At Slave Server:

Restore database backup taken on slave server using following command.

```
# mariadb -u root -p mydb < mydb-dump.sql
```
Login to mysql and execute following commands to reset slave state also.

```
mysql> RESET SLAVE;
mysql> CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=1;
```
After resetting slave start slave replication.

```
mysql> START SLAVE;
```

Now your replication has been re sync same as newly configured. you can verify it using the following commands.

```
mysql> show slave status \G
```

