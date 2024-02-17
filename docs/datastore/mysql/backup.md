---
layout: default
title: Backup
nav_order: 3
grand_parent: Datastore
parent: MySQL
---

# mysqldump

"mysqldump" provides highly useful backup options for MySQL backup processes.

Creating a user specifically for mysqldump would be beneficial. This is because assigning permissions tailored to the required functionalities is always optimal at any given time.

 * create user for mysqldump
```
GRANT SELECT, SHOW VIEW, LOCK TABLES, RELOAD, REPLICATION CLIENT ON *.* TO 'BACKUPUSER'@'localhost' IDENTIFIED BY 'password';
FLUSH PRIVILEGES;
```


Following is script for mysql backup. 

```
#!/bin/bash

GZIP_BIN=gzip
BACKUP_EXT=gz
DATE=$(date +%G%m%e%H%M%S)

DEFAULT_FILE=/path/backup/util/.mysqlbkup.cnf
BACKUP_DIR=/path/backup/mysql/
BACKUP_FILE="mydump_all.${DATE}.${BACKUP_EXT}"

mysqldump --defaults-file=${DEFAULT_FILE} -h url --all-databases --single-transaction | $GZIP_BIN > "$BACKUP_DIR/$BACKUP_FILE"

find $BACKUP_DIR -ctime +10 -exec rm -f {} \;

```

And, you can find many useful utility shell in many places

Following is most famous script.

[quickshiftin/mysqlbkup](https://github.com/quickshiftin/mysqlbkup)

