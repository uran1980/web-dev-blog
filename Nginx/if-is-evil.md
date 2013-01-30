`if` - это Зло!!!
==========
* **[Введение](#%D0%92%D0%B2%D0%B5%D0%B4%D0%B5%D0%BD%D0%B8%D0%B5)**
* **[Что на замену?](#%D0%A7%D1%82%D0%BE-%D0%BD%D0%B0-%D0%B7%D0%B0%D0%BC%D0%B5%D0%BD%D1%83)**
* **[Примеры](#%D0%9F%D1%80%D0%B8%D0%BC%D0%B5%D1%80%D1%8B)**
* **[Почему это до сих пор не исправлено?](#%D0%9F%D0%BE%D1%87%D0%B5%D0%BC%D1%83-%D1%8D%D1%82%D0%BE-%D0%B4%D0%BE-%D1%81%D0%B8%D1%85-%D0%BF%D0%BE%D1%80-%D0%BD%D0%B5-%D0%B8%D1%81%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%BE)**
* **[Если вы по-прежнему хотите использовать `if`](#%D0%95%D1%81%D0%BB%D0%B8-%D0%B2%D1%8B-%D0%BF%D0%BE-%D0%BF%D1%80%D0%B5%D0%B6%D0%BD%D0%B5%D0%BC%D1%83-%D1%85%D0%BE%D1%82%D0%B8%D1%82%D0%B5-%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D1%8C-if)**


## Введение
Директива **[`if`](http://nginx.org/ru/docs/http/ngx_http_rewrite_module.html#if)** имеет массу проблем при использовании внутри блока **[`location`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#location)**. Иногда, и это происходит очень часто, она не выполняет то, что от нее ожидают. В некоторых ситуация она может приводить к **[ошибкам сегментации памяти](http://ru.wikipedia.org/wiki/%D0%9E%D1%88%D0%B8%D0%B1%D0%BA%D0%B0_%D1%81%D0%B5%D0%B3%D0%BC%D0%B5%D0%BD%D1%82%D0%B0%D1%86%D0%B8%D0%B8)** и связанным с этим сбоям. Поэтому в большинстве случаем, если это возможно, лучше не использовать директиву `if` вообще.

Единственное 100% безопасное применение этой директивы в контексте блока `location` это:
* `return ...;`
* `rewrite ... last;`

Все остальные применения могут приводить к непредсказуемым результатам, включая **[SIGSEGV  (переполнение буфера)](http://ru.wikipedia.org/wiki/SIGSEGV)**.


It is important to note that the behaviour of if is not inconsistent, given two identical requests it will not randomly fail on one and work on the other, with proper testing and understanding ifs **can** be used. The advice to use other directives where available still very much apply, though.

There are cases where you simply cannot avoid using an if, for example if you need to test a variable which has no equivalent directive.

```nginx
if ( $request_method = POST ) {
  return 405;
}
if ( $args ~ post=140 ) {
  rewrite ^http://example.com/ permanent;
}
```
[к началу](#%D0%98%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%BE%D0%BF%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%B0-if-%D0%B2-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B5-location---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Что на замену?
Use **[try_files](http://nginx.org/ru/docs/http/ngx_http_core_module.html#try_files)** if it suits your needs. Use the `return ...` or `rewrite ... last` in other cases. In some cases it's also possible to move ifs to server level (where it's safe as only other rewrite module directives are allowed within it).

E.g. the following may be used to safely change location which will be used to process request:
```nginx
    location / {
        error_page 418 = @other;
        recursive_error_pages on;
 
        if ($something) {
            return 418;
        }
 
        # некоторые настройки
        ...
    }
 
    location @other {
        # некоторые другие настройки
        ...
    }
```

In some cases it may be good idea to use embedded scripting modules (**[embedded perl](http://nginx.org/ru/docs/http/ngx_http_perl_module.html)**, or various 3rd party modules) to do the scripting.

[к началу](#%D0%98%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%BE%D0%BF%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%B0-if-%D0%B2-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B5-location---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Примеры
Here are some examples which explain why if is evil. Don't try this at home. You were warned.
```nginx
        #########################################################################################
        #
        # Здесь собрана подборка примеров конфигураций nginx с проявлением непонятных багов,
        # связанных с использованием условного оператора if.
        # Эти примеры показывают что использование оператора if внутри блока location - это Зло!!!
        #
        ##########################################################################################
 
        #
        # Только второй заголовок будет в ответе сервера.
        # Вообще-то это не баг, просто так nginx это отрабатывает =)
        #
        location /only-one-if {
            set $true 1;
 
            if ($true) {
                add_header X-First 1;
            }
 
            if ($true) {
                add_header X-Second 2;
            }
 
            return 204;
        }
 
        #
        # запрос будет отправлен в бекэнд без добавления '/' в uri
        # (это результат работы оператора if)
        #
        location /proxy-pass-uri {
            proxy_pass http://127.0.0.1:8080/;
 
            set $true 1;
 
            if ($true) {
                # ничего
            }
        }
 
        #
        # директива try_files не сработает из-за наличия условного оператора if
        #
        location /if-try-files {
             try_files  /file  @fallback;
 
             set $true 1;
 
             if ($true) {
                 # ничего
             }
        }
 
        #
        # nginx will SIGSEGV
        #
        location /crash {
            set $true 1;
 
            if ($true) {
                # fastcgi_pass здесь...
                fastcgi_pass  127.0.0.1:9000;
            }
 
            if ($true) {
                # ничего
            }
        }
 
        #
        # alias не сработает корректно из-за наличия if
        #
        #
        location ~* ^/if-and-alias/(?<file>.*) {
            alias /tmp/$file;
 
            set $true 1;
 
            if ($true) {
                # ничего
            }
        }
```
[к началу](#%D0%98%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%BE%D0%BF%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%B0-if-%D0%B2-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B5-location---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Почему это до сих пор не исправлено?
Directive `if` is part of rewrite module which evaluates instructions imperatively. On the other hand, nginx configuration in general is declarative. At some point due to users demand an attempt was made to enable some non-rewrite directives inside `if`, and this lead to situation we have now. It mostly works, but... see above.

Looks like the only correct fix would be to disable non-rewrite directives inside if completely. It would break many configuration out there though, so wasn't done yet. 

[к началу](#%D0%98%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%BE%D0%BF%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%B0-if-%D0%B2-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B5-location---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Если вы по-прежнему хотите использовать `if`
If you read all of the above and still want to use `if`:
* Please make sure you actually do understand how it works. Some basic idea may be found e.g. *[here](http://agentzh.blogspot.ru/2011/03/how-nginx-location-if-works.html)**.
* Do proper testing. 

You were warned.

[к началу](#%D0%98%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-%D0%BE%D0%BF%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%B0-if-%D0%B2-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B5-location---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Оригинал статьи
* **[IfIsEvil](http://wiki.nginx.org/IfIsEvil)** (англ.)
