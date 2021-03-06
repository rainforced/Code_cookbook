# Что такое Docker
**Докер** - это движок, который запускает виртуальную операционную систему, имеющую чрезвычайно маленький вес (в отличие от Vagrant-а, который создаёт полноценную виртуальную ОС, Докер, имеет особые образы ПО, запускающиеся в виртуальной среде, не создавая полную копию ОС).    
- Как разработчик, теперь вы не должны волноваться о том, на какой системе будет запущено ваше приложение.
- Как пользователь, вам не нужно волноваться о том, что вы скачаете неподходящую версию ПО (нужного для работы программы). В Докере эта программа будет запущена в аналогичных условиях, при которых это приложение было разработано, потому, исключается факт получить какую-то новую, непредвиденную ошибку. Не нужно ставить последнюю версию JVM, NodeJS и тд, все уже предустановлено в докер-контейнере.    

**Docker образ** (он же **Docker Image**) - Docker имеет различные образы ПО: ubuntu, php (который наследуется от оригинального образа Ubuntu), nodejs, и т.д. Т.е. это облеченные версии ПО, которые можно использовать.  Так например, образ ubuntu весит только 74 мб. Docker Image (образ) - *неизменяемый*. 
```py
docker pull <IMAGE_NAME>
#где <IMAGE_NAME> - имя скачиваемого образа
docker pull ubuntu:18.10
#скачает образ Ubuntu 18.10:
```
Эта команда сообщает Докеру о том, что нужно скачать образ Ubuntu 18.10 с Dockerhub.com - основной репозиторий Docker-образов, на котором вы и можете посмотреть весь их список и подобрать нужный образ для вашей программы.     

Теперь, для того, чтобы посмотреть список всех загруженных образов, нужно выполнить:
```py
docker images
```
Чтобы проверить какие статус контейнеров 
```py
docker ps -a #без опции -a выведет только запущенные сейчас контейнеры
#c опции -a выведет ВСЕ контейнеры, в тч старые (закрытые)
```

# Docker-контейнер 
**Docker контейнер** - это экземпляр запущенного образа. Т.е. мы делаем копию докер-образа и что-то в ней делаем/меняем. После выполнения программы контейнер закрывается. 

## Команда `run`
Для запуска контейнера существует команда:
```py
docker run <image> <опциональная команды, которая выполнится внутри контейнера>
```
Запустим контейнер Ubuntu:
```py
docker run ubuntu:18.10 echo 'hello from ubuntu'
```
- Команда `echo 'hello from ubuntu'` была выполнена внутри среды Ubuntu. Другими словами, эта команда была выполнена в контейнере ubuntu:18.10. Т.е. запустилась мини-версия ubuntu, в ней выполнилась команда echo, и контейнер закрылся.     
- При каждом использовании `run` будет создаваться новый чистый контейнер с новым ID - уникальным номером контейнера.
- При этом, все старые контейнеры будут оставаться на локальном диске с состоянии, в котором мы их покинули. Т.е. если мы что-то поменяли в контейнере, то изменение в нем сохранится.

Docker контейнер является полностью независимым от системы хоста, из которой он запускался. Как изолированная виртуальная машина. И в ней вы можете производить любые изменения, которые никак не повлияют на основную операционную систему.

Мы можем подключиться к консоли виртуальной ОС (Ubuntu 18.10), и выполнять любое количество команд без завершения работы контейнера, для этого, запустим команду:
```py
docker run -it ubuntu:18.10 /bin/bash
```
Опция `-it` вместе с `/bin/bash` даёт доступ к выполнению команд в терминале внутри контейнера Ubuntu. Приэтом в имя пользователя (в командной строке) это **ID контейнера** те `root@0055a94c57f6` -> ID: `0055a94c57f6`.
- Вообще, команда `/bin/bash` запускает в Linux командную строку
- `-it` is short for `--interactive` + `--tty`. When you docker run with this command it takes you straight inside the container.
- `-d` is short for `--detach`, which means you just run the container and then detach from it. Essentially, you run container in the background.
- So if you run the Docker container with `-itd`, it runs both the `-it` options and detaches you from the container. As a result, your container will still be running in the background even without any default app to run.


```py
exit #выйти из контейнера
```

## Команда `start`
`start` запускает уже созданный контейнер
```py
docker start <CONTAINER_ID>
# где CONTAINER_ID - это id контейнера, который можно посмотреть, выполнив команду "docker ps -a"
```

## Команда `exec`
```py
docker start 7579c85c8b7e    #7579c85c8b7e - это id контейнера
docker exec -it 7579c85c8b7e /bin/bash  #запустили командную строку в контейнере
```
Команда `exec` позволяет выполнить команду внутри запущенного контейнера. В нашем случае, мы выполнили `/bin/bash`, что позволило нам подключиться к терминалу внутри контейнера.

## Удаление контейнеров
Теперь остановим и удалим Docker контейнеры командами:
```py
docker stop <CONTAINER_ID> # остановим активный контейнер
docker rm <CONTAINER_ID> # удалим контейнер
```
Например:
```py
docker ps -a   # просмотрим список активных контейнеров 
docker stop aa1463167766   # остановим активный контейнер
docker rm aa1463167766     # удалим контейнер
docker rm bb597feb7fbe     # удалим второй контейнер
```
В основном, нам не нужно, чтобы в системе плодилось большое количество контейнеров. Потому, команду `docker run` очень часто запускают с дополнительным флагом `--rm`, который удаляет запущенный контейнер после работы:
```py
docker run -it --rm ubuntu:18.10 /bin/bash
# выполнит операцию в контейнере и удалит при закрытии
```