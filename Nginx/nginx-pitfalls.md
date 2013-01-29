Nginx и подводные камни
=======================
If you're new or old to Nginx you will likely wind up in a pitfall situation. Below we outline these pitfalls and how to best avoid them. These are issues seen time and time again in the #nginx channel on Freenode. Please, just don't do this.


## This Guide Says
Don't follow the guides out there unless you can understand what they're trying to do and can clean it up. So many of the configs out there are horribly written. The Pitfalls page was written entirely from poor configs found on the Internet. They weren't found by searching but were instead found by support requests asking why something doesn't work. The common response is to disagree with what we say because some random guide says to do it the wrong way. That's what this page is designed to address. If you're having issues, this is the place to look.


## Root inside Location Block
**BAD:**
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

**GOOD:**
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


## Multiple Index Directives
**BAD:**
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

**GOOD:**
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


## Using If
There is a little page about using if statements. It's called **[IfIsEvil](http://wiki.nginx.org/IfIsEvil)** and you really should check it out. Let's take a look at a few uses of if that are bad.


## Server Name
**BAD:**
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

There are actually three problems here. The first being the if. That's what we care about now. Why is this bad? Did you read **[If is Evil?](http://wiki.nginx.org/IfIsEvil)** When nginx receives a request no matter what is the subdomain being requested, be it `www.domain.com` or just the plain `domain.com` this if directive is **always** evaluated. Since you're requesting nginx to check for the Host header for **every request**. It's extremely inefficient. You should avoid it. Instead use two server directives like the example below.

**GOOD:**
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

Besides making the configuration file easier to read. This approach decreases nginx processing requirements. We got rid of the spurious if. We're also using $scheme which doesn't hardcodes the URI scheme you're using, be it http or https.


## Check IF File Exists
Using if to ensure a file exists is horrible. It's mean. If you have any recent version of Nginx you should look at **[try_files](http://nginx.org/ru/docs/http/ngx_http_core_module.html#try_files)** which just made life much easier.

**BAD:**
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

**GOOD:**
```nginx
server {
  root /var/www/domain.com;
  location / {
    try_files $uri $uri/ /index.html;
  }
}
```

What we changed is that we try to see if $uri exists without requiring an if. Using `try_files` mean that you can test a sequence. If $uri doesn't exist, try `$uri/`, if that doesn't exist try a fallback location.

In this case it will see if the $uri file exists. If it does then serve it. If it doesn't then tests if that directory exists. If not, then it will proceed to serve index.html which you make sure exists. It's loaded but oh so simple. This is another instance you can completely eliminate If.


##Front Controller Pattern based packages
**Front Controller Pattern** designs are popular and used on the many of the most popular PHP software packages. A lot of examples are more complex than they need to be. To get Drupal, Joomla, etc. to work, just use this:
```nginx
try_files $uri $uri/ /index.php?q=$uri&$args;
```
Note - the parameter names are different based on the package you're using. For example:
* `q` is the parameter used by **[Drupal](http://ru.wikipedia.org/wiki/Drupal)**, **[Joomla](http://ru.wikipedia.org/wiki/Joomla)**, **[WordPress](http://ru.wikipedia.org/wiki/WordPress)** 
* `page` is used by **[CMS Made Simple](http://ru.wikipedia.org/wiki/CMS_Made_Simple)**

Some software doesn't even need the query string, and can read from REQUEST_URI (WordPress supports this, for example):
```nginx
try_files $uri $uri/ /index.php;
```

Of course, your mileage may vary and you may need more complex things based on your needs, but for a basic sites, these will work perfectly. You should always start simple and build from there.

You can also decide to skip the directory check and remove `$uri/` from it as well, if you don't care about checking for the existence of directories.


## Passing Uncontrolled Requests to PHP
Many example Nginx configurations for PHP on the web advocate passing every URI ending in `.php` to the PHP interpreter. Note that this presents a serious security issue on most PHP setups as it may allow arbitrary code execution by third parties.

The problem section usually looks like this:
```nginx
location ~* \.php$ {
  fastcgi_pass backend;
  ...
}
```


















## Оригинал статьи
* **[Nginx Pitfalls](http://wiki.nginx.org/Pitfalls)** (англ.)
