# SQL Databases. Fine Tuning and Optimization

Run ```docker-compose up -d``` to launch containers. 

Run ```composer install``` in php container to build dependencies.

Run ```docker exec -it l8-php-1 php /var/www/html/index.php ``` to run insert queries. 


Edit the USERS_NUMBER_TO_INSERT constant to set the number of users to insert. 

Set ```true``` for both CREATE_TABLE and INSERT_USERS constants if you run the script for the first time. Otherwise only INSERT_USERS should be set to ```true```

## Adding indexes
Btree index added to the dob column with the query: ```CREATE INDEX users_btree ON users (dob) USING BTREE;```
Query OK, 0 rows affected (4 min 11.75 sec)
Records: 0  Duplicates: 0  Warnings: 0

Hash index added to the dob column with the query:```CREATE INDEX users_btree ON users (dob) USING HASH;```
Query OK, 0 rows affected, 1 warning (4 min 7.13 sec)
Records: 0  Duplicates: 0  Warnings: 1

## Performance comparison
### Insert 10k users

#### No index
innodb_flush_log_at_trx_commit=1
3.299 sec

innodb_flush_log_at_trx_commit=2
3.285 sec

innodb_flush_log_at_trx_commit=0
7.924 sec 

#### Btree index
innodb_flush_log_at_trx_commit=1
85.963 sec

#### Hash index
innodb_flush_log_at_trx_commit=1
80.127 sec

### Insert 100k users

#### No index
innodb_flush_log_at_trx_commit=1
37.136 sec

innodb_flush_log_at_trx_commit=2
36.922 sec

innodb_flush_log_at_trx_commit=0
35.248 sec

### Select operations 
Query:
```select dob FROM users WHERE DATE(dob) > '2015-01-01' LIMIT 100000;```

#### No index
100000 rows in set (1.27 sec)

#### Btree index
100000 rows in set (15.92 sec)

#### Hash index
100000 rows in set (21.04 sec)

Query:
```select dob FROM users WHERE DATE(dob) > '2015-01-01' ORDER BY dob ASC LIMIT 100;```

#### No index
100 rows in set (2 min 42.87 sec)

#### Btree index
100 rows in set (9.30 sec)

#### Hash index
100 rows in set (4.31 sec)
