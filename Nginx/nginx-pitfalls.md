Конфигурация Nginx и подводные камни
====================================
* **[`Root` внутри блока `Location`](#root-%D0%B2%D0%BD%D1%83%D1%82%D1%80%D0%B8-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B0-location)**
* **[Повторяющиеся директивы `Index`](#%D0%9F%D0%BE%D0%B2%D1%82%D0%BE%D1%80%D1%8F%D1%8E%D1%89%D0%B8%D0%B5%D1%81%D1%8F-%D0%B4%D0%B8%D1%80%D0%B5%D0%BA%D1%82%D0%B8%D0%B2%D1%8B-index)**
* **[Ипсользование условия `If` - зло!!!](#%D0%98%D0%BF%D1%81%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D1%83%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D0%B9-if---%D0%B7%D0%BB%D0%BE)**
* **[Server Name](#server-name)**
* **[Проверка что файл cуществует](#%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D1%87%D1%82%D0%BE-%D1%84%D0%B0%D0%B9%D0%BB-c%D1%83%D1%89%D0%B5%D1%81%D1%82%D0%B2%D1%83%D0%B5%D1%82)**
* **[Шаблон *Front Controller* и CMS движки, которые его используют](#%D0%A8%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD-front-controller-%D0%B8-cms-%D0%B4%D0%B2%D0%B8%D0%B6%D0%BA%D0%B8-%D0%BA%D0%BE%D1%82%D0%BE%D1%80%D1%8B%D0%B5-%D0%B5%D0%B3%D0%BE-%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D1%83%D1%8E%D1%82)**
* **[Перенаправление всех запросов в PHP](#%D0%9F%D0%B5%D1%80%D0%B5%D0%BD%D0%B0%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%B2%D1%81%D0%B5%D1%85-%D0%B7%D0%B0%D0%BF%D1%80%D0%BE%D1%81%D0%BE%D0%B2-%D0%B2-php)**
* **[FastCGI путь в `SCRIPT_FILENAME`](#fastcgi-%D0%BF%D1%83%D1%82%D1%8C-%D0%B2-script_filename)**
* **[Проверяем rewrite'ы](#%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D1%8F%D0%B5%D0%BC-rewrite%D1%8B)**
* **[Перепрописываем пропущенные `http://`](#%D0%9F%D0%B5%D1%80%D0%B5%D0%BF%D1%80%D0%BE%D0%BF%D0%B8%D1%81%D1%8B%D0%B2%D0%B0%D0%B5%D0%BC-%D0%BF%D1%80%D0%BE%D0%BF%D1%83%D1%89%D0%B5%D0%BD%D0%BD%D1%8B%D0%B5-http)**
* **[Проксируем все подряд](#%D0%9F%D1%80%D0%BE%D0%BA%D1%81%D0%B8%D1%80%D1%83%D0%B5%D0%BC-%D0%B2%D1%81%D0%B5-%D0%BF%D0%BE%D0%B4%D1%80%D1%8F%D0%B4)**
* **[Изменения в конфиге не работают](#%D0%98%D0%B7%D0%BC%D0%B5%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B2-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D0%B5-%D0%BD%D0%B5-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D1%8E%D1%82)**
* **[Пропущенные или пропавшие *HTTP* заголовки]()**


Не важно новичек вы или опытный профессионал в настройке nginx, рано или позно вы можете столкнутся с непонятными проблемами при написании конфигов. Это подводные камни конфигурирования nginx и от них никуда не уйти. Далее в этой статье мы рассмотрим эти ситуации и способы их обхода.

В интернете полно некорректных, зачастую неправильных, инструкций по настройке nginx. Нельзя им слепо следовать не понимая, что выполняет и зачем служит та или иная настройка nginx. Множество инструкций и примеры конфигураций приведенные в них просто ужасны и не читабельны. Эта статья и появилась как ответ на такие инструкции, которые направляют новичков по ложному пути, запутывая их окончательно и заставля задавать одни и теже вопросы на форумах и **[IRC](http://ru.wikipedia.org/wiki/IRC)** каналах почему у них что-то не работает.

Итак, поехали.


## `Root` внутри блока `Location`
**ПЛОХО:**
```nginx
server {
  server_name www.domain.com;
  location / {
    root /var/www/nginx-default/;
    [...]
  }
  location /foo {
    root /var/www/nginx-default/;
    [...]
  }
  location /bar {
    root /var/www/nginx-default/;
    [...]
  }
}
```

Это работает. Помещать директиву [`root`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#root) внутри блока [`location`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#location) это нормально и правильно. Что не правильно в данном случае, дак это то, что директива `root` повторяется в каждом блоке `location`, следовательно если ни один из *локейшенов* не сработает, то у нас директива `root` вообще не будет задана. Правильным решение является вынос директивы `root` из блоков `location` в начало блока [`server`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#server). Ниже приведен пример как надо делать правильно.

**ХОРОШО:**
```nginx
server {
  server_name www.domain.com;
  root /var/www/nginx-default/;
  location / {
    [...]
  }
  location /foo {
    [...]
  }
  location /bar {
    [...]
  }
}
```

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Повторяющиеся директивы `Index`
**ПЛОХО:**
```nginx
http {
  index index.php index.htm index.html;
  server {
    server_name www.domain.com;
    location / {
      index index.php index.htm index.html;
      [...]
    }
  }
  server {
    server_name domain.com;
    location / {
      index index.php index.htm index.html;
      [...]
    }
    location /foo {
      index index.php;
      [...]
    }
  }
}
```

Зачем повторять столько строчек кода. Просто используйте директиву [`index`](http://nginx.org/ru/docs/http/ngx_http_index_module.html#index) один раз. Ее необходимо задать всего один раз внутри блока [`http`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#http) и она будет наследоваться всеми вложенными блоками.

**ХОРОШО:**
```nginx
http {
  index index.php index.htm index.html;
  server {
    server_name www.domain.com;
    location / {
      [...]
    }
  }
  server {
    server_name domain.com;
    location / {
      [...]
    }
    location /foo {
      [...]
    }
  }
}
```

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Ипсользование условий `If` - зло!!!
Есть небольшая статья по применению условного оператора [`if`](http://nginx.org/ru/docs/http/ngx_http_rewrite_module.html#if) внутри блоков [`location`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#location), описывающая почему так делать нельзя и это плохо. Статья так и называется **[Использование оператора if в блоке location - это Зло!!!](https://github.com/uran1980/my-blog/blob/master/Nginx/if-in-location-is-evil.md)**. Очень рекомендуем прочитать вам эту статью, и посмотреть на примерах почему оператор `if` это зло.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Server Name
**ПЛОХО:**
```nginx
server {
  server_name domain.com *.domain.com;
  if ($host ~* ^www\.(.+)) {
    set $raw_domain $1;
    rewrite ^/(.*)$ $raw_domain/$1 permanent;
    [...]
  }
}
```

На самом деле здесь несколько проблемных моментов... Первое это `if` (смотрим статью **[Использование оператора if в блоке location - это Зло!!!](https://github.com/uran1980/my-blog/blob/master/Nginx/if-in-location-is-evil.md)**). Когда nginx получает запрос, не важно в какой поддомен он направлен, будь это `www.domain.com` или `domain.com`, условие `if` **всегда сработает!!!** В примере получается, что мы указываем, чтобы nginx проверял заголовок хоста ([встроенную переменную](http://nginx.org/ru/docs/http/ngx_http_core_module.html#variables) `$host`) **при каждом запросе**. Это крайне не эффективно. Вы должны избегать этого. Вместо неудачного условия `if` лучше использовать два блока с директивой [`server`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#server), как в примере ниже.


**ХОРОШО:**
```nginx
server {
  server_name www.domain.com;
  return 301 $scheme://domain.com$request_uri;
}
server {
  server_name domain.com;
  [...]
}
```

В итоге мы получили читабильный конфиг. Так же, мы уменьшили затраты ресурсов nginx на обработку запросов. A еще мы избавились от условия `if` и использовали свтроенную переменную `$scheme`, которая позволяет не привязывать **[URI](http://ru.wikipedia.org/wiki/URI)** к определенному протоколу, будь то `http` или `https`.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Проверка что файл cуществует
Использовать `if` для проверкиналичия файла это просто ужасно. В nginx для этого есть специальная директива **[`try_files`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#try_files)**, которая делает жизнь на много проще.

**ПЛОХО:**
```nginx
server {
  root /var/www/domain.com;
  
  location / {
    if (!-f $request_filename) {
      break;
    }
  }
}
```

**ХОРОШО:**
```nginx
server {
  root /var/www/domain.com;
  
  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

Использование директивы `try_files` подразумевает что вы можете тестировать не одно у словие а последовательность. Например, в данном примере проверяется существует ли файл по указанному `$uri`, если файл не найден, то проверяется существование директории `$uri/`, если и ее нет, то проверка идет далее пока не сработает условие, конечное условие сработает по умолчанию (в данном примере это `/index.html`), если совпадение так и не найдтеся.
Вот еще один попвод чтобы избавится от `if`.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Шаблон *[Front Controller](http://www.martinfowler.com/eaaCatalog/frontController.html)* и CMS движки, которые его используют
Шаблон **Front Controller** сейчас очень популярен и используется во многих CMS движках и современных фреймворках на PHP. Далее показан простой пример, применимый для CMS Drupal, Joomla и др.:
```nginx
try_files $uri $uri/ /index.php?q=$uri&$args;
```

***

**ВНИМАНИЕ** Названия параметров могут отличаться для разных CMS. Например:
* `q` - используется в **[Drupal](http://ru.wikipedia.org/wiki/Drupal)**, **[Joomla](http://ru.wikipedia.org/wiki/Joomla)**, **[WordPress](http://ru.wikipedia.org/wiki/WordPress)**
* `page` - используется в **[CMS Made Simple](http://ru.wikipedia.org/wiki/CMS_Made_Simple)**

***

Некоторым движкам вообще не нужны параметры и они вытаскивают всю необходимую информацию из строки запроса `REQUEST_URI` (например WordPress):
```nginx
try_files $uri $uri/ /index.php;
```

Кончно, эти параметры для других CMS и фреймворков, здесь приведен общий пример. В некоторых систуациях вам понадится более комплексное решение, пробуйте и экспериментируйте...

Тажк вы можете вообще убрать проверку на наличие директории `$uri/`, если вам эта проверка не нужна.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Перенаправление всех запросов в PHP
Многие примеры конфигурации nginx для PHP рекомендуют все запросы оканчивающиеся на `.php` перенаправлять (проксировать) в интерпретатор PHP. Но это, вообще-то, потенциальная угроза безопасности на множестве конфигураций PHP, которая может привести к выполнению вредоносного кода на сервере.

Проблемная секция обычно выглядит так:
```nginx
location ~* \.php$ {
  fastcgi_pass backend;
  ...
}
```

Здесь каждый запрос оканчивающийся на `.php` перенаправляется в бекэнд на **[FastCGI](http://ru.wikipedia.org/wiki/FastCGI)**. Угроза безопасности здесь в том, что конфигурация PHP по-умолчанию всегда пытается выяснить какой файл вы хотите выполнить, даже если запрос идет на не существующий файл.

Например, вот запрос к файлу `/forum/avatar/1232.jpg/file.php` которого на самом деле нет на сервере, но зато на сервере есть файл `/forum/avatar/1232.jpg`. В данном случае по-умолчанию PHP обработает это файл `/forum/avatar/1232.jpg`. А теперь представьте, что в этом файле содержится вредоносный код для PHP? Соответсвенно он будет выполнен!

Чтобы это исключить, необходимо:
* Установить директиву `cgi.fix_pathinfo=0` в **php.ini**. Это заставит интерпретатор PHP остановиться на выполнении первого запроса и при отсутсвии запрашиваемого ресурса не идти далее по цепочке.
* Убедится что nginx отправляет в PHP на обработку только определенные файлы, расчитанные на такую обработку. А не все подряд:

```nginx
location ~* (file_a|file_b|file_c)\.php$ {
  fastcgi_pass backend;
  ...
}
```

* запретить обработку интерпретатором PHP всех файлов из директории куда пользователи загружают свои файлы:

```nginx
location /uploaddir {
  location ~ \.php$ {return 403;}
  ...
}
```

* Использовать директиву `try_files` чтобы отбросить запросы на не существующие файлы:

```nginx
location ~* \.php$ {
  try_files $uri =404;
  fastcgi_pass backend;
  ...
}
```

* Использовать вложенные секции `location`, чтобы исключитьпроблемные условия:

```nginx
location ~* \.php$ {
  location ~ \..*/.*\.php$ {return 404;}
  fastcgi_pass backend;
  ...
}
```

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## FastCGI путь в `SCRIPT_FILENAME`
Во многих руководствах по настройке nginx используются абсолютные пути для указания корневой директории сайта. Это частая практика в PHP блоках конфигурации nginx. Но в подключаемых конфигах для настройки парметров FastCGI `include fastcgi_params;` этого делать нельзя, для этого есть встроенная переменная `$document_root`.

**ХОРОШО:**
```nginx
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
```

**ПЛОХО:**
```nginx
fastcgi_param  SCRIPT_FILENAME    /var/www/yoursite.com/$fastcgi_script_name;
```

Вы спросите, где задается переменная `$document_root`? Она задается директивой [`root`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#root), которая должна присутствовать в блоке [`server`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#server). И если в вашем конфиге ее там нет, тогда вы наткнулись на первый подводный камень описанный нами в начале этой статьи: **[`Root` внутри блока `Location`](#root-%D0%B2%D0%BD%D1%83%D1%82%D1%80%D0%B8-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B0-location)** =)

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Проверяем rewrite'ы
Не считайте себя гуру в написании регулярных выражений, в любом случае вы ошибетесь и не заметите этого. В сложном регулярном выражении нельзя быть уверенным на сто процентов. Поэтому самое простое, это исключить такие выражения из использования, в пользу более чистого и понятного кода. Вот пример:

**ПЛОХО:**
```nginx
rewrite ^/(.*)$ http://domain.com/$1 permanent;
```

**ХОРОШО:**
```nginx
rewrite ^http://domain.com$request_uri? permanent;
```

**ЕЩЕ ЛУЧШЕ:**
```nginx
return 301 http://domain.com$request_uri;
```
Посмотрети, на первый вариант, потом на последний, потом опять на первый и опять на последний. Ну что? Первый *реврайт* запоминает полный путь без учета начального слеша и затем помещает этот путь вместо плейсхолдера `$1`. Однако используя встроенную переменную `$request_uri` мы спокойно можем избавится от всей этой магии с регулярными выражениями.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Перепрописываем пропущенные `http://`
Все очень просто. Реврайты - относительны, даже если вы указываете nginx обратное. Чтобы сделать реврайты абсолютными, просто добавьте тип http протокола в начало адреса. Смотрим пример:

**BAD:**
```nginx
rewrite ^domain.com permanent;
```

**ХОРОШО:**
```nginx
rewrite ^http://domain.com permanent;
```
В примере выше мы просто добавили в реврайт **http://**. Это очень просто и эффективно.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Проксируем все подряд
**ПЛОХО:**
```nginx
server {
    server_name _;
    root /var/www/site;
    location / {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass unix:/tmp/phpcgi.socket;
    }
}
```
**[FFFUUU](http://lurkmore.to/Ffruustration)**. В этом примере мы перенаправляем **ВСЕ ПОДРЯД!!!** в PHP. Зачем? Для Apache это простительно, но вам нет. Директива **[try_files](http://nginx.org/ru/docs/http/ngx_http_core_module.html#try_files)** придумана не просто так, она проверяет наличие запрашиваемого ресурса (файла) в определнной последовательности. Это значит, что nginx может проверить первое условие, потом второе и т.д. пока по цепочке не дойдет до конца. И если ничего не сработало, то по-умолчанию выполнится последннее условие в цепочке проверок. А это значит, что интерепретатор PHP будет вызываться не каждый раз а только тогда кода это действительно необходимо. Обработка запросов при этом ускорится многократно! 

Представьте, что идет несколько однотипных запросов на получение файла картинки размеров 1Мб и в примере выше каждый такой запрос будет перенаправлен в PHP. В примере ниже показано как избавиться от этого безобразия:

**ХОРОШО:**
```nginx
server {
    server_name _;
    root /var/www/site;
    location / {
        try_files $uri $uri/ @proxy;
    }
    location @proxy {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass unix:/tmp/phpcgi.socket;
    }
}
```

**ТОЖЕ ХОРОШО:**
```nginx
server {
    server_name _;
    root /var/www/site;
    location / {
        try_files $uri $uri/ /index.php;
    }
    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_pass unix:/tmp/phpcgi.socket;
    }
}
```
Просто, не правда-ли? Мы видим что, если запрашиваемый URI существует, то он будет отработан nginx. Если нет, то проверяется существует ли вообще такая директория `$uri/`. Если нет, то уже тогда, по-умолчанию, запрос будет направлен в `/index.php` и обработан PHP.

Теперь представьте сколько на вашем сайте статического контента (картинок, css-стилей, js-скриптов и т.д.), сколько лишних запросов вы отсекли от обработки через PHP?

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Изменения в конфиге не работают
Браузер кеширует запросы! Ваша конфигурация может быть идельной и без единой ошибки, но вы по-прежнему бъетесь головой об стену и пытаетесь понять почему у вас ничего не работает? Что не так? Ответ на этот вопрос: кешь браузера. Когда вы загружаете что-либо, браузер сохраняет это в своем кеше. Он также сохраняет то, как был обработан скаченный файл. Если вы в конфигурации nginx играетесь с блоками `{}`, скорее всего вы столкнетесь с проблемами кеширования запросов браузером.

#### Решение
* **[Вариант 1]** в Firefox нажмите **Ctrl+Shift+Delete**, проерьте кешь, нажмите кнопку **Очистить**. Если у вас другой браузер, то погуглите как очищается кешь в нем...
* **[Варинт 2]** Используйте для тестирования **[curl](http://ru.wikipedia.org/wiki/CURL)**.

#### VirtualBox
Если действия выше не помогают, и вы запускаете nginx на виртуальной машине, причина может быть в [`sendfile`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#sendfile). Просто закомментируйте директиву или установите ее в значение в `off`. Эта директива может располагаться где-то в вашем конфиге nginx в файле `nginx.conf`:
```
sendfile off;
```

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Пропущенные или пропавшие *HTTP* заголовки
If you do not explicitly set `underscores_in_headers on;`, nginx will silently drop HTTP headers with underscores (which are perfectly valid according to the HTTP standard). This is done in order to prevent ambiguities when mapping headers to CGI variables, as both dashes and underscores are mapped to underscores during that process.

Если вы явно не задавли директиву в значение `underscores_in_headers on;`, то nginx по-умолчанию будет тихо и незаметно пропускать HTTP заголовки, содержащие симолы нижнего подчеркиваеия (которые на самом деле с точки зрения описания протокола HTTP вполне допустимы). Nginx делает это только для исключения неоднознаности при маппинге заголовков в переменные при передаче в CGI скрипты, и преобразует символы `-` и `_` в символы нижнего подчеркивания `_`.

Более подробно на эту тему смотрите в **[официальной документации по nginx](http://nginx.org/ru/docs/http/ngx_http_core_module.html#underscores_in_headers)**


## Оригинал статьи
* **[Nginx Pitfalls](http://wiki.nginx.org/Pitfalls)** (англ.)
