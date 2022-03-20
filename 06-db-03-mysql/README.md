# Домашнее задание к занятию "6.3. MySQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/tree/master/additional/README.md).

## Задача 1

Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.
> **Делаю из под винды через Docker Desktop**
```
docker run --name mysql-max -v C:\Users\Max\06-db-03-mysql\mysql_db:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Kolhoz!100 -d mysql:8
```
Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-03-mysql/test_data) и 
восстановитесь из него.
> Нужно создать БД test_db перед восстановлением бэкапа
```bash
PS C:\Users\Max\06-db-03-mysql> docker exec -it mysql-max bash
root@ec9433c7bf08:/# mysql -uroot -p"$MYSQL_ROOT_PASSWORD"
> CREATE DATABASE test_db;
\q
```
> Под виндой пришлось прибегнуть к тому методу восстановдение БД
```
PS C:\Users\Max\06-db-03-mysql> cat .\test_dump.sql | docker exec -i mysql-max sh -c 'mysql -uroot -p"$MYSQL_ROOT_PASSWORD" USE test_db;'
```
Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h` получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из ее вывода версию сервера БД.

```
mysql> status
--------------
mysql  Ver 8.0.28 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          18
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.28 MySQL Community Server - GPL
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    latin1
Conn.  characterset:    latin1
UNIX socket:            /var/run/mysqld/mysqld.sock
Binary data as:         Hexadecimal
Uptime:                 15 hours 45 min 17 sec

Threads: 2  Questions: 79  Slow queries: 0  Opens: 145  Flush tables: 3  Open tables: 63  Queries per second avg: 0.001
--------------
```
Подключитесь к восстановленной БД и получите список таблиц из этой БД.

```
mysql> USE test_db;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SHOW TABLES;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.00 sec)
```
**Приведите в ответе** количество записей с `price` > 300.

```
mysql> SELECT COUNT(*) FROM orders WHERE price > 300;
+----------+
| COUNT(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```
В следующих заданиях мы будем продолжать работу с данным контейнером.

## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:
- плагин авторизации mysql_native_password
- срок истечения пароля - 180 дней 
- количество попыток авторизации - 3 
- максимальное количество запросов в час - 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James"

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.

```
mysql> CREATE USER 'test'@'localhost'IDENTIFIED WITH mysql_native_password BY 'test-pass';
Query OK, 0 rows affected (0.06 sec)

mysql> ALTER USER 'test'@'localhost' PASSWORD EXPIRE INTERVAL 180 DAY FAILED_LOGIN_ATTEMPTS 3;
Query OK, 0 rows affected (0.02 sec)

mysql> ALTER USER 'test'@'localhost' WITH MAX_QUERIES_PER_HOUR 100;
Query OK, 0 rows affected (0.01 sec)

mysql> ALTER USER 'test'@'localhost' ATTRIBUTE '{"Surname":"Pretty", "Name":"James"}';
Query OK, 0 rows affected (0.00 sec)

mysql> GRANT SELECT ON test_db.* TO 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.01 sec)
```
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю `test` и 
**приведите в ответе к задаче**.

```
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER='test';
+------+-----------+----------------------------------------+
| USER | HOST      | ATTRIBUTE                              |
+------+-----------+----------------------------------------+
| test | localhost | {"Name": "James", "Surname": "Pretty"} |
+------+-----------+----------------------------------------+
1 row in set (0.00 sec)
```

## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

```
mysql> SELECT TABLE_NAME, ENGINE FROM information_schema.TABLES where TABLE_SCHEMA = 'test_db';
+------------+--------+
| TABLE_NAME | ENGINE |
+------------+--------+
| orders     | InnoDB |
+------------+--------+
1 row in set (0.00 sec)
```

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`
- на `InnoDB`

```
mysql> SHOW PROFILES;
+----------+------------+-----------------------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                                   |
+----------+------------+-----------------------------------------------------------------------------------------+
|        1 | 0.00119200 | SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER='test'                      |
|        2 | 0.00112000 | SELECT TABLE_NAME, ENGINE FROM information_schema.TABLES where TABLE_SCHEMA = 'test_db' |
|        3 | 0.11167600 | ALTER TABLE orders ENGINE = MyISAM                                                      |
|        4 | 0.06762900 | ALTER TABLE orders ENGINE = InnoDB                                                      |
+----------+------------+-----------------------------------------------------------------------------------------+
4 rows in set, 1 warning (0.01 sec)
```

## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):
- Скорость IO важнее сохранности данных
- Нужна компрессия таблиц для экономии места на диске
- Размер буффера с незакомиченными транзакциями 1 Мб
- Буффер кеширования 30% от ОЗУ
- Размер файла логов операций 100 Мб

Приведите в ответе измененный файл `my.cnf`.

```
innodb_flush_log_at_trx_commit = 2
innodb_file_per_table = ON
innodb_log_buffer_size = 1M
innodb_buffer_pool_size = 2G
innodb-log-file-size = 100M
```

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
