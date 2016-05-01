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

Теперь, чтобы использовать команды Docker без необходимости каждый раз вводить ```sudo``` добавим текущего пользователя под которым мы залогинены в группу ```docker```, эта группа создается автоматически при установке Docker:
```
sudo usermod -aG docker <ваше_текущее_имя_пользователя>
```

после этого перезагружаем компьютер.

Также для теста работы Docker можно выполнить:
```
sudo docker run hello-world
```

На экране должно вывестись:
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
Ознакомимся с базовыми операциями установленного клиента Docker.

Вывод зпаущенных контейнеров:
```
docker ps -a
```

В консоли выведится список контейнеров с базовой информацией по ним:
```
Output
    CONTAINER ID    IMAGE           COMMAND     CREATED         STATUS                      PORTS   NAMES
    a3b149c3ddea    hello-world     "/hello"    3 minutes ago   Exited (0) 3 minutes ago            nostalgic_hopper
```

Если имя контенера не было задано заранее, то Docker сгенериреут произвольное имя. В данном случае это **nostalgic_hopper**. Также видно что контейнер ```hello-world``` был запущен 3 минуты назад.

Теперь попробуем удалить тестовый контейнер:
```
docker rm nostalgic_hopper
```

На следующем шаге мы начнем использовать наш Nginx контейнер...


##Шаг 3 - Экпорт портов сервера
Рассмотрим теперь как скачать Docker-образ Nginx сервера, как его запустить и сделать доступным извне web-сервером.

Чтобы сделать контейнер доступным извне, необходимо открыть его внутренние порты.
Для начала скачаем готовый образ Nginx с официального хранилища образов для Docker:
```
docker pull nginx
```

Начнется загрузка официального образа Nginx с [Docker Hub](https://hub.docker.com/explore/). Docker Hub это отрытое хранилище официальных и неофициальных образов различных приложений.

Запустить скаченный образ можно командой:
```
docker run --name docker-nginx -p 8080:80 nginx
```

* ```run``` -- команда для создания и запуска нового контейнера
* ```--name``` -- парметр для задания имени создаваемого контенера
* ```-p``` -- парметр указвающий проброс портов в фомате ```-p локальный_порт:внутренний_порт_контейнера```, в данном примере мы пробрасываем 80 внутрениий порт контенера на 8080 порт нашего локального сервера
* ```nginx``` -- имя образа который необходимо запустить в качестве Docker-контейнера

Это все что необходимо для запуска контейнера. Теперь откройте свой браузер и введите в адресной строке следующий адрес: **http://localhost:8080** вы увидите страницу приветсвия сервера Nginx, запущенного в контейнере Docker.

Обратите внимание, что в консоли процесс запуска контейнера остался запущенным. Т.е. пока не нажать ```CTRL+C``` и не завершить работу запущенного контейнера, мы не сможем в конслои вводить другие команды.

Давайте теперь удалим созданный контайнер:
```
docker rm docker-nginx
```

На следующем шаге мы научимся запускать контейнер в фоновом режиме (**Detached Mode**)...


##Шаг 4 - Запуск контейнера в фоновом режиме (Detached Mode)
Создадим новый контейнер и запустим его в фоновом режиме, чтобы он работал бескончено долго в фоне:
```
docker run --name docker-nginx -p 8080:80 -d nginx
```

Мы добавили параметр ```-d``` чтобы контейнер работал в фоновом режиме (режим **Detached Mode**).

Посмотрим теперь информацию о запущенном контейнере:
```
docker ps
```

Теперь мы увидим что-то такое:
```
Output
CONTAINER ID    IMAGE   COMMAND                  CREATED              STATUS              PORTS                         NAMES
b91f3ce26553    nginx   "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   docker-nginx
```

Теперь мы имеем запущенный в фоне контейнер Nginx сервера доступный по адресу: **http://localhost:8080**

Чтобы остановить контейнер запустите:
```
docker stop docker-nginx
```

Теперь, когда контейнер остановлен мы можем удалить его:
```
docker rm docker-nginx
```


##Шаг 5 - Создаем html страницу для Nginx
На этом шаге давайте создади тестовую html-страничку, которую вдальнейшем будет обрабатывать сервер Nginx.

Страничку будем создавать в нашей домашней директории ```/home/your_user_name/www/docker-nginx/html```
Файл странички ```index.html``` будет содержать следующий html-код:
```
<html>
  <head>
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css" rel="stylesheet" integrity="sha256-MfvZlkHCEqatNoGiOXveE8FIwMzZg4W85qfrfIFBfYc= sha512-dTfge/zgoMYpP7QbHy4gWMEGsbsdZeCXz7irItjcC3sPUFtf0kuFbDz/ixG7ArTxmDjLXDmezHubeNikyKGVyQ==" crossorigin="anonymous">
    <title>Docker nginx Tutorial</title>
  </head>
  <body>
    <div class="container">
      <h1>Hello Digital Ocean</h1>
      <p>This nginx page is brought to you by Docker and Digital Ocean</p>
    </div>
  </body>
</html>
```



##Шаг 6 - Свзязываем контейнер с локальной файловой системой
На данном шаге соберем все вместе и проверим работу контейнера Nginx. Docker позволяет делать ссылки на директории на локальном сервере и связывать их с директориями внутри контейнера.

По умолчанию Nginx сервер ищет индексную html страницу ```index.html``` по следующему пути внутри своего контейнера: ```/usr/share/nginx/html```.

Чтобы связать фалй на локальном сервере с файлами внутри контейнера создадим новый контейнер со следующими параметрами:
```
docker run --name docker-nginx -p 8080:80 -d -v ~/www/docker-nginx/html:/usr/share/nginx/html nginx
```

* ```-v``` -- параметр который задает перелинковку файловых систем локального хоста и контейнера
* путь слева от ```:``` это путь к файлу/каталогу на локальном сервере (```~/docker-nginx/www/html```)
* путь справа от ```:``` это путь к файлу/каталогу внутри контейнера (```/usr/share/nginx/html```)

После запуска этой команды, перейдите в браузере по адресу ```http://localhost:8080```, вы увидите вместо приветсвия Nginx html странияку созданную нами ранее на 5 шаге.


##Шаг 7 - Свой конфигруационный файл для Nginx
Давайте вернемся обратно в наш каталог на локальном сервере:
```
cd ~/docker-nginx
```

Чтобы посмотреть на конфигурационный файл Nginx используемый по умолчанию контейнером, даваете его скопируем в наш каталог:
```
docker cp docker-nginx:/etc/nginx/conf.d/default.conf ~/docker-nginx/default.conf
```

Т.к. мы хотим вдальнейшем использовать свой конфигурационный файл Nginx, давайте пересоберем контейнер:
```
docker stop docker-nginx
docker rm docker-nginx
```

Теперь мы можем отредактировать скопированный ранее конфигурационный файл ```~/docker-nginx/default.conf``` и заново запустить контейнерер Nginx:
```
docker run --name docker-nginx -p 8080:80 -v ~/docker-nginx/html:/usr/share/nginx/html -v ~/docker-nginx/default.conf:/etc/nginx/conf.d/default.conf -d nginx
```

Eсли вдальнейшем Вы отредактируете конфигурационный файл то не забудьте перезагрузить веб сервер, чтобы изменения вступили в силу:
```
docker restart docker-nginx
```


##Заключение
Итак, мы получили работающий Docker-контейнер с запущенным web-сервером Nginx.


## Ссылки
* **[Оригинал статьи](https://www.digitalocean.com/community/tutorials/how-to-run-nginx-in-a-docker-container-on-ubuntu-14-04)**  (англ.)
* **[Связваание контейнеров между собой](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/)**
* **[компонеты экосистемы  Docker](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-an-introduction-to-common-components)**
