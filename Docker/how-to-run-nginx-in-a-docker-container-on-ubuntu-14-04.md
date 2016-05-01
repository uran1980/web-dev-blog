Запуск Nginx внутри Docker-контейнера на Ubuntu 14.04
=====================================================


##Начало
Эта статья рассказывает как развернуть сервер Nginx в Docker-контейнере.

Помещая Nginx в контейнер Docker мы упрощаем процесс дальнейшего администрирования. Нам больше нет необходимости поддерживать сервер Nginx через пакетные менеджеры или устанавливать из исходников для нашей операционной системы. Docker-контейнер позволяет нам просто заменять целый контейнер при появлении новой версии сервера Nginx. При этом нам необходимо будет работать только с конфигурауионными файлами Nginx и web-контентом который сервер Nginx будет обслуживать.

На практике системные администраторы используют сервер Nginx для обслуживания web-контента от простенького статичного сайта до больших API систем построенных, к примеру, на [NodeJS](https://nodejs.org/en/).

Docker-контейнеры это распространенный способ использования относительно давней практики контенирезации окружений. Контейнеризация отличается от виртуализации тем что виртуализация абстрагируется от железа сервера, в то время как контенеризация абстрагируется от базовой операционной системы сервера.
Это подразумевает, что мы можем взять любое приложение (или группу приложений) и объединить их в один или несколько виртуальных контейнеров для обеспечения модульности и возможности портирования на различные операционные системы.

Получается, что мы можем установить движок Docker на любую операционную систему и использовать созданные контейнеры на этих системах.


##Подготовка
Для создания Docker-контейнера Nginx нам понадобится операционная система Ubuntu 14.04 или выше, пользователь имеющий возможность выполнять команды через ```sudo``` и установленный движок Docker.


##Шаг 1 - Установка Docker
На официальном сайте [Docker](https://docs.docker.com/linux/step_one/) подробно описан процесс установки.
Нам необходимо в консоли выполноить комнаду:
```
sudo curl -sSL https://get.docker.com/ | sh
```

После установки наберите команду:
```
docker --version
```

в консоли должно отобразиться что-то типа этой записи:
```
Docker version 1.11.1, build 5604cbe
```

Теперь чтобы использовать команды Docker без необходимости каждый раз вводить ```sudo``` необходимо текущего пользователя под которым мы залогинены добавить в группу ```docker``` которая была создана автоматически при установке Docker. Для этого выполните команду:
```
sudo usermod -aG docker <ваше_текущее_имя_пользователя>
```

после этого необходимо перезагрузить компьютер.

Также для теста работы Docker можно выполнить эту команду:
```
sudo docker run hello-world
```

На экране должно вывестись это:
```
Output
    $ docker run hello-world
    Unable to find image 'hello-world:latest' locally
    latest: Pulling from library/hello-world
    535020c3e8ad: Pull complete
    af340544ed62: Already exists
    library/hello-world:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Digest: sha256:d5fbd996e6562438f7ea5389d7da867fe58e04d581810e230df4cc073271ea52
    Status: Downloaded newer image for hello-world:latest

    Hello from Docker.
    This message shows that your installation appears to be working correctly.

    To generate this message, Docker took the following steps:
     1. The Docker client contacted the Docker daemon.
     2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
     3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
     4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

    To try something more ambitious, you can run an Ubuntu container with:
     $ docker run -it ubuntu bash

    Share images, automate workflows, and more with a free Docker Hub account:
     https://hub.docker.com

    For more examples and ideas, visit:
     https://docs.docker.com/userguide/
```


##Шаг 2 - Обзор базовых операций: Run, List, Remove
TODO


##Шаг 3 - Экпорт портов сервера
TODO


##Шаг 4 - Запуск контейнера в фоновом режиме (Detached Mode)
TODO


##Шаг 5 - Создаем html страницу для Nginx
TODO


##Шаг 6 - Свзязываем контейнер с локальной файловой системой
TODO


##Шаг 7 - Свой конфигруационный файл для Nginx
TODO


##Заключение
TODO


## Ссылки
* **[Оригинал статьи](https://www.digitalocean.com/community/tutorials/how-to-run-nginx-in-a-docker-container-on-ubuntu-14-04)**  (англ.)
* **[компонеты экосистемы  Docker](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-an-introduction-to-common-components)**
