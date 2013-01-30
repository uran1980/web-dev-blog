Условия `if` в конфигах nginx - это Зло!!!
==========================================
* **[Введение](#%D0%92%D0%B2%D0%B5%D0%B4%D0%B5%D0%BD%D0%B8%D0%B5)**
* **[Что на замену?](#%D0%A7%D1%82%D0%BE-%D0%BD%D0%B0-%D0%B7%D0%B0%D0%BC%D0%B5%D0%BD%D1%83)**
* **[Примеры](#%D0%9F%D1%80%D0%B8%D0%BC%D0%B5%D1%80%D1%8B)**
* **[Почему это до сих пор не исправлено?](#%D0%9F%D0%BE%D1%87%D0%B5%D0%BC%D1%83-%D1%8D%D1%82%D0%BE-%D0%B4%D0%BE-%D1%81%D0%B8%D1%85-%D0%BF%D0%BE%D1%80-%D0%BD%D0%B5-%D0%B8%D1%81%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%BE)**
* **[Если вы по-прежнему хотите использовать `if`](#%D0%95%D1%81%D0%BB%D0%B8-%D0%B2%D1%8B-%D0%BF%D0%BE-%D0%BF%D1%80%D0%B5%D0%B6%D0%BD%D0%B5%D0%BC%D1%83-%D1%85%D0%BE%D1%82%D0%B8%D1%82%D0%B5-%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D1%8C-if)**


## Введение
Directive **[`if`](http://nginx.org/ru/docs/http/ngx_http_rewrite_module.html#if)** has problems when used in **[`location`](http://nginx.org/ru/docs/http/ngx_http_core_module.html#location)** context, in some cases it doesn't do what you expect but something completely different instead. In some cases it even segfaults. It's generally a good idea to avoid it if possible.

The only 100% safe things which may be done inside `if` in `location` context are:
* [return](http://nginx.org/ru/docs/http/ngx_http_rewrite_module.html#return) ...;
* [rewrite](http://nginx.org/ru/docs/http/ngx_http_rewrite_module.html#rewrite) ... last; 

Anything else may possibly cause unpredictable behaviour, including potential **[SIGSEGV](http://ru.wikipedia.org/wiki/SIGSEGV)**.

It is important to note that the behaviour of if is not inconsistent, given two identical requests it will not randomly fail on one and work on the other, with proper testing and understanding ifs **can** be used. The advice to use other directives where available still very much apply, though.

There are cases where you simply cannot avoid using an if, for example if you need to test a variable which has no equivalent directive.

```nginx
if ($request_method = POST ) {
  return 405;
}
if ($args ~ post=140){
  rewrite ^ http://example.com/ permanent;
}
```
[к началу](#%D0%A3%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D1%8F-if-%D0%B2-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D0%B0%D1%85-nginx---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Что на замену?
TODO

[к началу](#%D0%A3%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D1%8F-if-%D0%B2-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D0%B0%D1%85-nginx---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Примеры
TODO

[к началу](#%D0%A3%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D1%8F-if-%D0%B2-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D0%B0%D1%85-nginx---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Почему это до сих пор не исправлено?
TODO

[к началу](#%D0%A3%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D1%8F-if-%D0%B2-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D0%B0%D1%85-nginx---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Если вы по-прежнему хотите использовать `if`
TODO

[к началу](#%D0%A3%D1%81%D0%BB%D0%BE%D0%B2%D0%B8%D1%8F-if-%D0%B2-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D0%B0%D1%85-nginx---%D1%8D%D1%82%D0%BE-%D0%97%D0%BB%D0%BE)


## Оригинал статьи
* **[IfIsEvil](http://wiki.nginx.org/IfIsEvil)** (англ.)
