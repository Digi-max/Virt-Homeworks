
# Домашнее задание к занятию "5.2. Применение принципов IaaC в работе с виртуальными машинами"

## Как сдавать задания

Обязательными к выполнению являются задачи без указания звездочки. Их выполнение необходимо для получения зачета и диплома о профессиональной переподготовке.

Задачи со звездочкой (*) являются дополнительными задачами и/или задачами повышенной сложности. Они не являются обязательными к выполнению, но помогут вам глубже понять тему.

Домашнее задание выполните в файле readme.md в github репозитории. В личном кабинете отправьте на проверку ссылку на .md-файл в вашем репозитории.

Любые вопросы по решению задач задавайте в чате учебной группы.

---

## Задача 1

- Опишите своими словами основные преимущества применения на практике IaaC паттернов.
> Паттерн позволяет привести к стандартизации процессов создания/изменения инфраструктуры. Ускорение процессов и минимизация ошибок.
- Какой из принципов IaaC является основополагающим?
> Так называемый **идемпотентность** - повторяющиеся операции с идентичным результатом. Своими словами - отработанный процесс с отличным результатом )

## Задача 2

- Чем Ansible выгодно отличается от других систем управление конфигурациями?
> управление по **SSH**
- Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?
> думаю метод **push**, т.к. управление в большей степени висит на сис админе. Может более безопасно.

## Задача 3

Установить на личный компьютер:

- VirtualBox
```bash
digi-max@home:~$ vboxmanage --version
6.1.26_Ubuntur145957
```
- Vagrant
```bash
digi-max@home:~$ vagrant -v
Vagrant 2.2.14
```
- Ansible
```bash
digi-max@home:~$ ansible --version
ansible 2.10.5
  config file = None
  configured module search path = ['/home/digi-max/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.9.7 (default, Sep 10 2021, 14:59:43) [GCC 11.2.0]
```
*Приложить вывод команд установленных версий каждой из программ, оформленный в markdown.*

## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

- Создать виртуальную машину.
- Зайти внутрь ВМ, убедиться, что Docker установлен с помощью команды
```
docker ps
```
> Так как явлюяюсь Windows пользователем, пришлось настроить среду в VirtualBox. По умолчанию подводные камни... В итоге запустил, но всё равно криво :blush:
Показываю часть баша:
```bash
==> server1.netology: Machine booted and ready!
==> server1.netology: Checking for guest additions in VM...
==> server1.netology: Setting hostname...
==> server1.netology: Configuring and enabling network interfaces...
==> server1.netology: Mounting shared folders...
    server1.netology: /vagrant => /home/digi-max/vagrant
==> server1.netology: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> server1.netology: flag to force provisioning. Provisioners marked to run always will still run.
digi-max@home:~/vagrant$
digi-max@home:~/vagrant$
digi-max@home:~/vagrant$
digi-max@home:~/vagrant$ vagrant provision
==> server1.netology: Running provisioner: ansible...
    server1.netology: Running ansible-playbook...

PLAY [nodes] *******************************************************************

TASK [Gathering Facts] *********************************************************
ok: [server1.netology]

TASK [Create directory for ssh-keys] *******************************************
ok: [server1.netology]

TASK [Adding rsa-key in /root/.ssh/authorized_keys] ****************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: If you are using a module and expect the file to exist on the remote, see the remote_src option
fatal: [server1.netology]: FAILED! => {"changed": false, "msg": "Could not find or access '~/.ssh/id_rsa.pub' on the Ansible Controller.\nIf you are using a module and expect the file to exist on the remote, see the remote_src option"}
...ignoring

TASK [Checking DNS] ************************************************************
changed: [server1.netology]

TASK [Installing tools] ********************************************************
ok: [server1.netology] => (item=['git', 'curl'])

TASK [Installing docker] *******************************************************
changed: [server1.netology]

TASK [Add the current user to docker group] ************************************
changed: [server1.netology]

PLAY RECAP *********************************************************************
server1.netology           : ok=7    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=1

digi-max@home:~/vagrant$ vagrant ssh
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

vagrant@server1:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
vagrant@server1:~$
```
