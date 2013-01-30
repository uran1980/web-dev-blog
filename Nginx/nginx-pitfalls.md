Конфигурация Nginx и подводные камни
====================================
* **[`Root` внутри блока `Location`](#root-%D0%B2%D0%BD%D1%83%D1%82%D1%80%D0%B8-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B0-location)**
* **[Повторяющиеся директивы `Index`](#%D0%9F%D0%BE%D0%B2%D1%82%D0%BE%D1%80%D1%8F%D1%8E%D1%89%D0%B8%D0%B5%D1%81%D1%8F-%D0%B4%D0%B8%D1%80%D0%B5%D0%BA%D1%82%D0%B8%D0%B2%D1%8B-index)**
* **[Ипсользование условия `If` - зло!!!](#%D0%98%D0%BF%D1%81%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D1%83%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D0%B9-if---%D0%B7%D0%BB%D0%BE)**
* **[Server Name](#server-name)**
* **[Проверка что файл cуществует](#%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D1%87%D1%82%D0%BE-%D1%84%D0%B0%D0%B9%D0%BB-c%D1%83%D1%89%D0%B5%D1%81%D1%82%D0%B2%D1%83%D0%B5%D1%82)**
* **[Шаблон *Front Controller* и CMS движки, которые его используют](#%D0%A8%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD-front-controller-%D0%B8-cms-%D0%B4%D0%B2%D0%B8%D0%B6%D0%BA%D0%B8-%D0%BA%D0%BE%D1%82%D0%BE%D1%80%D1%8B%D0%B5-%D0%B5%D0%B3%D0%BE-%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D1%83%D1%8E%D1%82)**
* **[Перенаправление неконтролируемых запросов в PHP](#%D0%9F%D0%B5%D1%80%D0%B5%D0%BD%D0%B0%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%BD%D0%B5%D0%BA%D0%BE%D0%BD%D1%82%D1%80%D0%BE%D0%BB%D0%B8%D1%80%D1%83%D0%B5%D0%BC%D1%8B%D1%85-%D0%B7%D0%B0%D0%BF%D1%80%D0%BE%D1%81%D0%BE%D0%B2-%D0%B2-php)**
* **[FastCGI путь в `SCRIPT_FILENAME`](#fastcgi-%D0%BF%D1%83%D1%82%D1%8C-%D0%B2-script_filename)**
* **[Проверяем рерайты](#%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D1%8F%D0%B5%D0%BC-%D1%80%D0%B5%D1%80%D0%B0%D0%B9%D1%82%D1%8B)**
* **[Перепрописываем пропущенные `http://`](#%D0%9F%D0%B5%D1%80%D0%B5%D0%BF%D1%80%D0%BE%D0%BF%D0%B8%D1%81%D1%8B%D0%B2%D0%B0%D0%B5%D0%BC-%D0%BF%D1%80%D0%BE%D0%BF%D1%83%D1%89%D0%B5%D0%BD%D0%BD%D1%8B%D0%B5-http)**
* **[Проксируем все подряд](#%D0%9F%D1%80%D0%BE%D0%BA%D1%81%D0%B8%D1%80%D1%83%D0%B5%D0%BC-%D0%B2%D1%81%D0%B5-%D0%BF%D0%BE%D0%B4%D1%80%D1%8F%D0%B4)**
* **[Изменения в конфиге не работают](#%D0%98%D0%B7%D0%BC%D0%B5%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B2-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D0%B5-%D0%BD%D0%B5-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D1%8E%D1%82)**
* **[Пропущенные (пропавшие) *HTTP* заголовки](#%D0%9F%D1%80%D0%BE%D0%BF%D1%83%D1%89%D0%B5%D0%BD%D0%BD%D1%8B%D0%B5-%D0%BF%D1%80%D0%BE%D0%BF%D0%B0%D0%B2%D1%88%D0%B8%D0%B5-http-%D0%B7%D0%B0%D0%B3%D0%BE%D0%BB%D0%BE%D0%B2%D0%BA%D0%B8)**


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

Это работает. Помещать директиву [`root`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#root) внутри блока [`location`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#location) это нормально и правильно. Что не правильно в данном случае, дак это то, что директива `root` повторяется в каждом блоке `location`, следовательно если ниодин из *локейшенов* не сработает, то у нас директива `root` вообще не будет задана. Правильным решение является вынос директивы `root` из блоков `location` в начало блока [`server`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#server). Ниже приведен пример как надо делать правильно.

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
Есть небольшая статья по применению условного оператора [`if`](http://nginx.org/ru/docs/http/ngx_http_rewrite_module.html#if) внутри блоков [`location`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#location), описывающая почему так делать нельзя и это зло. Статья так и называется **[Использование оператора if в блоке location - это Зло!!!](https://github.com/uran1980/my-blog/blob/master/Nginx/if-in-location-is-evil.md)**. Очень рекомендуем прочитать вам эту статью, и посмотреть на примерах почему оператор `if` внутри блока `location` это плохо.

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
**Front Controller Pattern** designs are popular and used on the many of the most popular PHP software packages. A lot of examples are more complex than they need to be. To get Drupal, Joomla, etc. to work, just use this:

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

Of course, your mileage may vary and you may need more complex things based on your needs, but for a basic sites, these will work perfectly. You should always start simple and build from there.

Кончно, эти параметры для других CMS и фреймворков, здесь приведен общий пример. В некоторых систуациях вам понадится более комплексное решение, пробуйте и экспериментируйте...

Тажк вы можете вообще убрать проверку на наличие директории `$uri/`, если вам эта проверка не нужна.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Перенаправление неконтролируемых запросов в PHP
Многие примеры конфигурации nginx для PHP рекомендуют все запросы оканчивающиеся на `.php` перенаправлять (проксировать) в интерпретатор PHP. Но это, вообще-то, потенциальная угроза безопасности на множестве конфигураций PHP, которая может привести к выполнению вредоносного кода на сервере.

Проблемная секция обычно выглядит так:
```nginx
location ~* \.php$ {
  fastcgi_pass backend;
  ...
}
```

Здесь каждый запрос оканчивающийся на `.php` перенаправляется в бекэнд на **[FastCGI](http://ru.wikipedia.org/wiki/FastCGI)**. Угроза безопасности здесь в том, что конфигурация PHP по-умолчанию всегда пытается выяснить какой файл вы хотите выполнить, даже если запрос идет на не существующий файл.

Например, вот запрос к файлу `/forum/avatar/1232.jpg/file.php` которого на самом деле нет на сервере, но зато на сервере есть файл `/forum/avatar/1232.jpg`. В данном случае по-умолчанию PHP обработает файл `/forum/avatar/1232.jpg`. А теперь представьте, что в этом файле содержится вредоносный код для PHP? Соответсвенно он будет выполнен!

Чтобы это исключить, необходимо:
* Установить директиву `cgi.fix_pathinfo=0` в **php.ini**. Это заставит интерпретатор PHP оставиться на выполнении первого запроса и при отсутсвии файл не идти далее по цепочке.
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
So many guides out there like to rely on absolute paths to get to your information. This is commonly seen in PHP blocks. When you install Nginx from a repository you'll usually wind up being able to toss **include fastcgi_params;** in your config. This is a file located in your Nginx root directory which is usually around `/etc/nginx/`.

**ХОРОШО:**
```nginx
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
```

**ПЛОХО:**
```nginx
fastcgi_param  SCRIPT_FILENAME    /var/www/yoursite.com/$fastcgi_script_name;
```
Where is `$document_root` set? It's set by the root directive that should be in your server block. Is your `root` directive not there? See the **[first pitfall](#root-%D0%B2%D0%BD%D1%83%D1%82%D1%80%D0%B8-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B0-location)**.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Проверяем рерайты
Don't feel bad here, it's easy to get confused with regular expressions. In fact, it's so easy to do that we should make an effort to keep them neat and clean. Quite simply, don't add cruft.

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
Look at the above. Then back here. Then up, and back here. OK. The first rewrite captures the full URI minus the first slash. By using the built-in variable `$request_uri` we can effectively avoid doing any capturing or matching at all. 

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Перепрописываем пропущенные `http://`
Very simply, rewrites are relative unless you tell nginx that they're not. Making a rewrite absolute is simple. Add a scheme.

**BAD:**
```nginx
rewrite ^domain.com permanent;
```

**ХОРОШО:**
```nginx
rewrite ^http://domain.com permanent;
```
In the above you will see that all we did was add **http://** to the rewrite. It's simple, easy, and effective.

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
Yucky. In this instance, you pass **EVERYTHING** to PHP. Why? Apache might do this, you don't need to. Let me put it this way... The **[try_files](http://nginx.org/ru/docs/http/ngx_http_core_module.html#try_files)** directive exists for an amazing reason. It tries files in a specific order. This means that Nginx can first try to server the static content. If it can't, then it moves on. This means PHP doesn't get involved at all. **MUCH** faster. Especially if you're serving a 1MB image over PHP a few thousand times versus serving it directly. Let's take a look at how to do that.

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
It's easy, right? You see if the requested URI exists and can be served by Nginx. If not, is it a directory that can be served. If not, then you pass it to your proxy. Only when Nginx can't serve that requested URI directly does your proxy overhead get involved.

Now.. consider how much of your requests are static content, such as images, css, javascript, etc. That's probably a lot of overhead you just saved.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Изменения в конфиге не работают
Browser cache. Your configuration may be perfect but you'll sit there and beat your head against a cement wall for a month. What's wrong is your browser cache. When you download something, your browser stores it. It also stores how that file was served. If you are playing with a types `{}` block you'll encounter this.

**The fix:**
* **[Option 1]** In Firefox press **Ctrl+Shift+Delete**, check Cache, click Clear Now. In any other browser just ask your favorite search engine. Do this after every change (unless you know it's not needed) and you'll save yourself a lot of headaches.
* **[Option 2]** Use **[curl](http://ru.wikipedia.org/wiki/CURL)**.

**VirtualBox**
If this does not work, and you're running nginx on a virtual machine in VirtualBox, it may be `sendfile()` that is causing the trouble. Simply comment out the sendfile directive or set it to "off". The directive is most likely found in your nginx.conf file.
```
sendfile off;
```

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Пропущенные (пропавшие) *HTTP* заголовки
If you do not explicitly set `underscores_in_headers on;`, nginx will silently drop HTTP headers with underscores (which are perfectly valid according to the HTTP standard). This is done in order to prevent ambiguities when mapping headers to CGI variables, as both dashes and underscores are mapped to underscores during that process. 


## Оригинал статьи
* **[Nginx Pitfalls](http://wiki.nginx.org/Pitfalls)** (англ.)
