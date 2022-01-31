
# Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера"

## Как сдавать задания

Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.

Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.

Домашнее задание выполните в файле readme.md в github репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

Любые вопросы по решению задач задавайте в чате учебной группы.

---

## Задача 1

Сценарий выполения задачи:

- создайте свой репозиторий на https://hub.docker.com;
- выберете любой образ, который содержит веб-сервер Nginx;
- создайте свой fork образа;
- реализуйте функциональность:
запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.

**Ответ**:
```bash
ubuntu@ip-172-31-41-139:~$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
5eb5b503b376: Pull complete
1ae07ab881bd: Pull complete
78091884b7be: Pull complete
091c283c6a66: Pull complete
55de5851019b: Pull complete
b559bad762be: Pull complete
Digest: sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```
Мой манифест и его код
```bash
ubuntu@ip-172-31-41-139:~$ ls
Dockerfile  site-content

FROM nginx
COPY /site-content /usr/share/nginx/html
```
Файл с нашей страничкой
```bash
ubuntu@ip-172-31-41-139:~/site-content$ ls
index.html
```
Создаю свой образ
```bash
ubuntu@ip-172-31-41-139:~$ docker build -t my-content-nginx .
Sending build context to Docker daemon  17.41kB
Step 1/2 : FROM nginx
 ---> c316d5a335a5
Step 2/2 : COPY /site-content /usr/share/nginx/html
 ---> 4c1bd083fc4b
Successfully built 4c1bd083fc4b
Successfully tagged my-content-nginx:latest
```
Меняю тег и пушу
```bash
ubuntu@ip-172-31-41-139:~$ docker tag ab237ced5d5d digimax1/netology/my-ngnix:v1
ubuntu@ip-172-31-41-139:~$ docker push digimax1/my-content-nginx:v1
The push refers to repository [docker.io/digimax1/my-content-nginx]
a43aa3d6d58b: Pushed
762b147902c0: Mounted from library/nginx
235e04e3592a: Mounted from library/nginx
6173b6fa63db: Mounted from library/nginx
9a94c4a55fe4: Mounted from library/nginx
9a3a6af98e18: Mounted from library/nginx
7d0ebbe3f5d2: Mounted from library/nginx
v1: digest: sha256:2a3b5892f1d172914b8a23714a7f5abd7dae636e2e414d7eae7d5460630d5cab size: 1777
```
**Итого, ссылка на образ**
https://hub.docker.com/r/digimax1/my-content-nginx
## Задача 2

Посмотрите на сценарий ниже и ответьте на вопрос:
"Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.
> Сразу скажу своё мнение - каждая компания предъявляет свои требования и условия к ландшафту ИТ (человеческие ресурсы, бюджет, масштабы).

--

Сценарий:

- Высоконагруженное монолитное java веб-приложение;
> Так как одно приложение, то это физический сервер. Можно и вируталку.
- Nodejs веб-приложение;
> Если простое приложение, то контейнер
- Мобильное приложение c версиями для Android и iOS;
> Насколько я вычитал, контейнеры нельзя использовать в iOS, а Android можно. Тогда выходит виртуальная машина
- Шина данных на базе Apache Kafka;
> Виртуальные или физические машины. Здесь нужно горизонтальное масштабирлвание.
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
> по-моему, кластер всегда подразумевает несколько физических машин
- Мониторинг-стек на базе Prometheus и Grafana;
> у меня мало опыта, чтобы описать почему, но вроде контейнеры подходят для этого случая
- MongoDB, как основное хранилище данных для java-приложения;
> Подойдёт виртуальная машина. Хотя много мнений, что для БД лучше использовать физику. Но тут зависит от возможностей компании.
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.
> Хранилище отдельно на физике или виртуалке, а остальное от возможностей компании.

## Задача 3

- Запустите первый контейнер из образа ***centos*** c любым тэгом в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл любого содержания в ```/data```;
- Добавьте еще один файл в папку ```/data``` на хостовой машине;
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` контейнера.

```bash
docker run -d -t -v /data:/data centos bash
docker run -d -t -v /data:/data debian:latest bash

docker ps -a
CONTAINER ID   IMAGE           COMMAND   CREATED        STATUS        PORTS     NAMES
54fa3476ef02   debian:latest   "bash"    30 hours ago   Up 29 hours             nostalgic_khorana
254f2cbb6509   centos          "bash"    30 hours ago   Up 29 hours             adoring_babbage

docker exec -d adoring_babbage touch /data/centos.txt
docker exec -d nostalgic_khorana touch /data/debian.txt

docker exec -it adoring_babbage /bin/sh
sh-4.4# cd data
sh-4.4# ls
centos.txt  debian.txt  host.txt

```

## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

Соберите Docker образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с остальными ответами к задачам.


---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
