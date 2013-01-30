Очень полезные команды Linux на одном листе
===========================================

### Системная информация
| Команда                 | Описание      |
|:------------------------|:--------------|
| `arch`                  | отобразить архитектуру компьютера |
| `uname -m`              | отобразить архитектуру компьютера |
| `uname -r`              |  отобразить используемую версию ядра |
| `dmidecode -q`          |	показать аппаратные системные компоненты - ([SMBIOS](http://ru.wikipedia.org/wiki/SMBIOS)/[DMI](http://ru.wikipedia.org/wiki/Direct_Media_Interface)) |
| `hdparm -i /dev/hda`    | вывести характеристики жесткого диска |
| `hdparm -tT /dev/sda`   | протестировать производительность чтения данных с жесткого диска |
| `cat /proc/cpuinfo`     | отобразить информацию о процессоре
| `cat /proc/interrupts`  | показать прерывания |
| `cat /proc/meminfo`     | проверить использование памяти |
| `cat /proc/swaps`       | показать файл(ы) подкачки |
| `cat /proc/version`     | вывести версию ядра |
| `cat /proc/net/dev`     | показать сетевые интерфейсы и статистику по ним |
| `cat /proc/mounts`      | отобразить смонтированные файловые системы |
| `lspci -tv`             | показать в виде дерева [PCI](http://ru.wikipedia.org/wiki/PCI) устройства |
| `lsusb -tv`             | показать в виде дерева [USB](http://ru.wikipedia.org/wiki/USB) устройства |
| `date`                  | вывести системную дату |
| `cal 2007`              | вывести таблицу-календарь 2007-го года |
| `date 041217002007.00`  | установить системные дату и время ММДДЧЧммГГГГ.СС (МесяцДеньЧасМинутыГод.Секунды) |
| `clock -w`              | сохранить системное время в [BIOS](http://ru.wikipedia.org/wiki/BIOS) |

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### Остановка системы
| Команда                       | Описание      |
|:------------------------------|:--------------|
| `shutdown -h now`             | Остановить систему |
| `init 0`                      | Остановить систему |
| `telinit 0`                   | Остановить систему |
| `shutdown -h hours:minutes &` | запланировать остановку системы на указанное время |
| `shutdown -c`                 | отменить запланированную по расписанию остановку системы |
| `shutdown -r now`             | перегрузить систему |
| `reboot`                      | перегрузить систему |
| `logout`                      | выйти из системы |

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### Файлы и директории
| Команда                 | Описание      |
|:------------------------|:--------------|
| `cd /home` |   перейти в директорию `/home`
| `cd ..` | 	перейти в директорию уровнем выше
| `cd ../..` | 	перейти в директорию двумя уровнями выше
| `cd` | 	перейти в домашнюю директорию
| `cd ~user` | 	перейти в домашнюю директорию пользователя user
| `cd -` | 	перейти в директорию, в которой находились до перехода в текущую директорию
| `pwd` | 	показать текущюю директорию
| `ls` | 	отобразить содержимое текущей директории
| `ls -F` | 	отобразить содержимое текущей директории с добавлением к именам символов, храктеризующих тип
| `ls -l` | 	показать детализированое представление файлов и директорий в текущей директории
| `ls -a` | 	показать скрытые файлы и директории в текущей директории
| `ls *[0-9]*` | 	показать файлы и директории содержащие в имени цифры
| `tree` | 	показать дерево файлов и директорий, начиная от корня (`/`)
| `lstree` | показать дерево файлов и директорий, начиная от корня (`/`)
| `mkdir dir1` | 	создать директорию с именем `dir1`
| `mkdir dir1 dir2` | 	создать две директории одновременно
| `mkdir -p /tmp/dir1/dir2` | 	создать дерево директорий
| `rm -f file1` | 	удалить файл с именем `file1`
| `rmdir dir1` | 	удалить директорию с именем `dir1`
| `rm -rf dir1` | 	удалить директорию с именем `dir1` и рекурсивно всё её содержимое
| `rm -rf dir1 dir2` | 	удалить две директории и рекурсивно их содержимое
| `mv dir1 new_dir` | 	переименовать или переместить файл или директорию
| `cp file1 file2` | 	сопировать файл `file1` в файл `file2`
| `cp dir/* .` | 	копировать все файлы директории dir в текущую директорию
| `cp -a /tmp/dir1 .` | 	копировать директорию `dir1` со всем содержимым в текущую директорию
| `cp -a dir1 dir2` | 	копировать директорию `dir1` в директорию `dir2`
| `ln -s file1 lnk1` | 	создать символическую ссылку на файл или директорию
| `ln file1 lnk1` | 	создать "жёсткую" (физическую) ссылку на файл или директорию
| `touch -t 0712250000 fileditest` | 	модифицировать дату и время создания файла, при его отсутствии, создать файл с указанными датой и временем (YYMMDDhhmm) 

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


### TODO
| Команда                 | Описание      |
|:------------------------|:--------------|
TODO

[к началу](#%D0%9E%D1%87%D0%B5%D0%BD%D1%8C-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D1%8B-linux-%D0%BD%D0%B0-%D0%BE%D0%B4%D0%BD%D0%BE%D0%BC-%D0%BB%D0%B8%D1%81%D1%82%D0%B5)


**[Оригинал](http://www.f-notes.info/linux:linux_command)**

















