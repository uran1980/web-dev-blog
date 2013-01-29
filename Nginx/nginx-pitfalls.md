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











## Оригинал статьи
* **[Nginx Pitfalls](http://wiki.nginx.org/Pitfalls)** (англ.)
