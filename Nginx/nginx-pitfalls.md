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
* **[Изменения в конфиге не работают](#%D0%98%D0%B7%D0%BC%D0%B5%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B2-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D0%B5-%D0%BD%D0%B5-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D1%8E%D1%82)**
* **[Пропущенные (пропавшие) *HTTP* заголовки](#%D0%9F%D1%80%D0%BE%D0%BF%D1%83%D1%89%D0%B5%D0%BD%D0%BD%D1%8B%D0%B5-%D0%BF%D1%80%D0%BE%D0%BF%D0%B0%D0%B2%D1%88%D0%B8%D0%B5-http-%D0%B7%D0%B0%D0%B3%D0%BE%D0%BB%D0%BE%D0%B2%D0%BA%D0%B8)**


If you're new or old to Nginx you will likely wind up in a pitfall situation. Below we outline these pitfalls and how to best avoid them. These are issues seen time and time again in the #nginx channel on **[Freenode](http://ru.wikipedia.org/wiki/Freenode)**. Please, just don't do this.


## В этой инструкции сказано
Don't follow the guides out there unless you can understand what they're trying to do and can clean it up. So many of the configs out there are horribly written. The Pitfalls page was written entirely from poor configs found on the Internet. They weren't found by searching but were instead found by support requests asking why something doesn't work. The common response is to disagree with what we say because some random guide says to do it the wrong way. That's what this page is designed to address. If you're having issues, this is the place to look.


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

This works. Putting root inside of a location block will work and it's perfectly valid. What's wrong is when you start adding location blocks. If you add a root to every location block then a location block that isn't matched will have no root. Let's look at a good configuration.

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

Why repeat so many lines when not needed. Simply use the **index** directive one time. It only needs to occur in your http `{ }` block and it will be inherited below.

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
There is a little page about using if statements. It's called **[IfIsEvil](http://wiki.nginx.org/IfIsEvil)** and you really should check it out. Let's take a look at a few uses of if that are bad.

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

There are actually three problems here. The first being the `if`. That's what we care about now. Why is this bad? Did you read **[If is Evil?](http://wiki.nginx.org/IfIsEvil)** When nginx receives a request no matter what is the subdomain being requested, be it `www.domain.com` or just the plain `domain.com` this `if` directive is **always** evaluated. Since you're requesting nginx to check for the Host header for **every request**. It's extremely inefficient. You should avoid it. Instead use two server directives like the example below.

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

Besides making the configuration file easier to read. This approach decreases nginx processing requirements. We got rid of the spurious `if`. We're also using `$scheme` which doesn't hardcodes the URI scheme you're using, be it *http* or *https*.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Проверка что файл cуществует
Using `if` to ensure a file exists is horrible. It's mean. If you have any recent version of Nginx you should look at **[try_files](http://nginx.org/ru/docs/http/ngx_http_core_module.html#try_files)** which just made life much easier.

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

What we changed is that we try to see if `$uri` exists without requiring an `if`. Using `try_files` mean that you can test a sequence. If `$uri` doesn't exist, try `$uri/`, if that doesn't exist try a fallback location.

In this case it will see if the `$uri` file exists. If it does then serve it. If it doesn't then tests if that directory exists. If not, then it will proceed to serve `index.html` which you make sure exists. It's loaded but oh so simple. This is another instance you can completely eliminate `If`.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Шаблон *[Front Controller](http://www.martinfowler.com/eaaCatalog/frontController.html)* и CMS движки, которые его используют
**Front Controller Pattern** designs are popular and used on the many of the most popular PHP software packages. A lot of examples are more complex than they need to be. To get Drupal, Joomla, etc. to work, just use this:
```nginx
try_files $uri $uri/ /index.php?q=$uri&$args;
```

***

**ВНИМАНИЕ:** The parameter names are different based on the package you're using. For example:
* `q` is the parameter used by **[Drupal](http://ru.wikipedia.org/wiki/Drupal)**, **[Joomla](http://ru.wikipedia.org/wiki/Joomla)**, **[WordPress](http://ru.wikipedia.org/wiki/WordPress)** 
* `page` is used by **[CMS Made Simple](http://ru.wikipedia.org/wiki/CMS_Made_Simple)**

***

Some software doesn't even need the query string, and can read from REQUEST_URI (WordPress supports this, for example):
```nginx
try_files $uri $uri/ /index.php;
```

Of course, your mileage may vary and you may need more complex things based on your needs, but for a basic sites, these will work perfectly. You should always start simple and build from there.

You can also decide to skip the directory check and remove `$uri/` from it as well, if you don't care about checking for the existence of directories.

[к началу](#%D0%9A%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8F-nginx-%D0%B8-%D0%BF%D0%BE%D0%B4%D0%B2%D0%BE%D0%B4%D0%BD%D1%8B%D0%B5-%D0%BA%D0%B0%D0%BC%D0%BD%D0%B8)


## Перенаправление неконтролируемых запросов в PHP
Many example Nginx configurations for PHP on the web advocate passing every URI ending in `.php` to the PHP interpreter. Note that this presents a serious security issue on most PHP setups as it may allow arbitrary code execution by third parties.

The problem section usually looks like this:
```nginx
location ~* \.php$ {
  fastcgi_pass backend;
  ...
}
```

Here, every request ending in `.php` will be passed to the **[FastCGI](http://ru.wikipedia.org/wiki/FastCGI)** backend. The issue with this is that the default PHP configuration tries to guess which file you want to execute if the full path does not lead to an actual file on the filesystem.

For instance, if a request is made for `/forum/avatar/1232.jpg/file.php` which does not exist but if `/forum/avatar/1232.jpg` does, the PHP interpreter will process `/forum/avatar/1232.jpg` instead. If this contains embedded PHP code, this code will be executed accordingly.

Options for avoiding this are:
* Set `cgi.fix_pathinfo=0` in **php.ini**. This causes the PHP interpreter to only try the literal path given and to stop processing if the file is not found. 
* Ensure that Nginx only passes specific PHP files for execution.

```nginx
location ~* (file_a|file_b|file_c)\.php$ {
  fastcgi_pass backend;
  ...
}
```

* Specifically disable the execution of PHP files in any directory containing user uploads.

```nginx
location /uploaddir {
  location ~ \.php$ {return 403;}
  ...
}
```

* Use the `try_files` directive to filter out the problem condition

```nginx
location ~* \.php$ {
  try_files $uri =404;
  fastcgi_pass backend;
  ...
}
```

* Use a nested location to filter out the problem condition

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
Proxy Everything

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
Yucky. In this instance, you pass **EVERYTHING** to PHP. Why? Apache might do this, you don't need to. Let me put it this way... The `try_files` directive exists for an amazing reason. It tries files in a specific order. This means that Nginx can first try to server the static content. If it can't, then it moves on. This means PHP doesn't get involved at all. **MUCH** faster. Especially if you're serving a 1MB image over PHP a few thousand times versus serving it directly. Let's take a look at how to do that.

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
