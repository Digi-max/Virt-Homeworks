# Домашнее задание к занятию "6.2. SQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/tree/master/additional/README.md).

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.
>Ответ
```yml
version: "3.9"
services:
  postgres:
    image: postgres:12
    environment:
      POSTGRES_DB: "maxdb"
      POSTGRES_USER: "maxuser"
      POSTGRES_PASSWORD: "maxuser"
      PGDATA: "/var/lib/postgresql/data/pgdata"
    volumes:
      - ./InitDatabaseScripts:/docker-entrypoint-initdb.d
      - .:/var/lib/postgresql/data
      - ./BACKUP:/var/lib/postgresql/data/backup
    ports:
      - "5432:5432"
```
```bash
docker-compose up
```

## Задача 2

В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
- создайте пользователя test-simple-user  
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)

Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:
- итоговый список БД после выполнения пунктов выше,
<p align="center">
  <img src=".\2022-03-16_021147.jpg">
</p>
- описание таблиц (describe)
<p align="center">
  <img src=".\2022-03-16_021536.jpg">
</p>
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
```sql
SELECT grantee,table_catalog, table_schema, table_name, privilege_type FROM information_schema.table_privileges WHERE table_name = 'orders' or table_name = 'clients';
```

- список пользователей с правами над таблицами test_db

<p align="center">
  <img src=".\2022-03-16_022538.jpg">
</p>


## Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
- приведите в ответе:
    - запросы 
    - результаты их выполнения.

<p align="center">
  <img src=".\2022-03-16_022954.jpg">
</p>

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
 
Подсказк - используйте директиву `UPDATE`.

```
update  clients set order_num = 3 where id = 1;
update  clients set order_num = 4 where id = 2;
update  clients set order_num = 5 where id = 3;
```
```
select * from clients as c where exists (select id from orders as o where c.order_num = o.id) ;
```
<p align="center">
  <img src=".\2022-03-16_115910.jpg">
</p>

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

<p align="center">
  <img src=".\2022-03-16_120252.jpg">
</p>

> Показывает нагрузку на запросы с разбивкой по шагам (план выполенения)
> Получается операция с таблицей orders заняла больше всего времени

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

```bash
root@48bc9753beae:~# pg_dump -U maxuser -W maxdb > /var/lib/postgresql/data/BACKUP/maxdb.sql
```
Остановите контейнер с PostgreSQL (но не удаляйте volumes).

```bash
docker-compose stop
```
Поднимите новый пустой контейнер с PostgreSQL.

```
version: "3.9"
services:
  postgres:
    image: postgres:12
    environment:
      POSTGRES_DB: "maxdb"
      POSTGRES_USER: "maxuser"
      POSTGRES_PASSWORD: "maxuser"
      PGDATA: "/var/lib/postgresql/data/pgdata"
    volumes:
      - .:/var/lib/postgresql/data
      - ../postgresql/BACKUP:/var/lib/postgresql/data/backup
    ports:
      - "5432:5432"
```

Восстановите БД test_db в новом контейнере.

```bash
docker exec -i postgresql2_postgres_1 psql -U maxuser -d maxdb -f /var/lib/postgresql/data/backup/maxdb.sql
```
Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

> Так как pg_dump не выгружает роли, то нужно использовать pg_dumpall
---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
