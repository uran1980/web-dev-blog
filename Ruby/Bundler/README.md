Bundler - менеджер пакетов (джемов) для Ruby
============================================

## Начальная настройка

необходимо, чтобы был установлен Ruby (для Windows можно скачать от [сюда](http://rubyinstaller.org/)).
> **ВНИМАНИЕ!!!** Для Ubuntu 12.04 надо ставить Ruby 1.9.3:
>
> ```
> sudo apt-get install ruby1.9.1 ruby1.9.1-dev
> ```

После успешной установки Ruby можно установить [Bandler](http://bundler.io/) с попмощью команды:


**Установка**
```
# для Linux:
sudo gem install bundler

# для Windows:
gem install bundler
```

**Настройка проекта**
В корне проекта нужно создать файл с именем ```Gemfile``` например такого содеражния:
```ruby
# @see http://bundler.io/
# bundle install
#
source 'https://rubygems.org'

# required ruby gems for project
gem 'sass'
gem 'compass'
gem 'knife-solo'
gem 'librarian'
gem 'capistrano', '~> 3.0.1'
```


## Полезные Ресурсы
* **[bundler.io](http://bundler.io/#getting-started)** - официальная странчка проекта


