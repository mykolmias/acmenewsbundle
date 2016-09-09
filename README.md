Acme News Project
========================

Данный проект - результат выполнения следующего тестового задания:

>     Задание N1
> 
>     Сделать на Symfony 2 небольшое приложение, состоящее из одного бандла Новости (AcmeNewsBundle),
>     который должен уметь:
>     1. Выводить список новостей с постраничной навигацией по ссылке вида /news?page=2 в формате HTML.
>     При этом верстка должна наиболее полно отражать семантическое представление данных.
>     2. Тоже самое, но в формате XML при запросе по ссылке вида /news.xml?page=2. При этом получаемый
>     XML-файл должен быть валидным, иметь корректные заголовки и структуру вида:
>     <list>
>         <item id="идентификатор новости" date="дата новости">
>                 <announce>Краткий текст новости</announce>
>             <description>Полный текст новости</description>
>         </item>
>         <item>
>             ....
>         </item>
>         </list>
>     3. Выводить полнотекст новости в HTML-виде по ссылке вида /news/123, где 123 - ID новости.
>     Под текстом новости должен быть блок со списком любых других новостей, который может
>     присутствовать и на других страницах сайта.
> 
>     В процессе выполнения задания необходимо показать знания и навыки владения OOP,
>     ORM Doctrine 2 и Symfony 2 в целом.
> 
>     Исходные данные - таблица с полями:
>         1. Идентификатор новости
>         2. Дата новости
>         3. Опубликовано да/нет
>         4. Краткий текст новости
>         5. Полный текст новости
>      
>     Решение необходимо представить в виде готового проекта, опубликованного на github, содержащего:
>        1. сущности, репозитории, сервисы и т.д.
>        2. бандл AcmeNewsBundle
>        3. само приложение
>        4. дамп БД
>        5. в readme проекта должны быть описаны шаги развертывания и любые ваши комментарии

Ознакомиться с работой приложения можно по адресу http://swivl.this.biz/

Структура проекта
--------------

Основная функциональность сосредоточена в бандле AcmeNewsBundle.

На уровне приложения хранятся и используются, главным образом:

  * конфигурационные файлы приложения app/config;

  * миграции БД;

  * настройки PHPUnit;

Установка
--------------

### Установка для локальной  разработки с использованием Vagrant

* Создать произвольную папку на компьютере, где будут храниться исходные коды
* Перейти в созданную папку, выполнить 
```
git clone https://github.com/TZSwivl/acmenewsbundle.git .
```

* Прописать в /etc/hosts (или в аналоге в Windows) запись
```
192.168.33.10 tzswivl.com www.tzswivl.com
```
* Запустить виртуальную машину командой
```
vagrant up
```
* Зайти в консоль виртуальной машины и
  * залить дампы в уже созданные Vagrant'ом mysql базы данных
  * загрузить зависимости проекта composer'ом
```
# Для sss пароля не потребуется
vagrant ssh
cd www
mysql -u dbuser -pveryverystrongandlongpassword symfony < /home/ubuntu/www/app/DbMigrations/1.sql 
mysql -u dbuser -pveryverystrongandlongpassword symfony < /home/ubuntu/www/app/DbMigrations/2.sql 
php composer.phar install
```
После выполнения указанных действий сайт должен быть доступен в браузере по адресу http://tzswivl.com/.

###Установка на произвольный хост

####Требования к серверу:
* web-server (nginx, apache)
* mysql-client, mysql-server
* php >= 7.0 (+ php7.0-xml php7.0-intl php7.0-mysql, php-memcached)
* memcached
* unzip

####Порядок установки
* В root папке вашего веб-сервера выполнить
```
git clone https://github.com/TZSwivl/acmenewsbundle.git .
php composer.phar install
```
* Создать пустые базы данных symfony, symfony_test и пользователя со всеми привелегиями в этих базах.
* Залить в них дампы 1.sql и 2.sql из папки app/DbMigrations
    
Настройка приложения
--------------
1. В разделе acme_news в файле конфигурации app/config/config.php можно исправить настройки бандла AcmeNewsBundle, 
заданные по умолчанию, если они вас не устраивают.
В частности, количество новостей на страницах html и xml лент новостей, в блоке дополнительных новостей, 
настройки кеширования.
2. Можно импортировать свежие новости в БД из rss ленты http://news.liga.net/all/rss.xml выполнив консольную команду
```
php bin/console acme:news:import:rss
```
    
Тестирование
--------------
Запуск тестов нужно выполнять с помощью команды
```
php phpunit.phar
```
Если запускать тесты системной утилитой phpunit, могут выскакивать ошибки с жалобами на обращение 
к необъявленным методам в классах тестов.

Примечания
--------------
###Избыточный функционал

Ниже описаны особенности и решения, которые не являются необходимыми для реализации требуемого в задании функционала.

Более того, в качестве акта "преждевременной оптимизации" они могли бы прямо вредить процессу 
разработки продукта/приложения в в реальных условиях.

Однако, в условиях выполнения тестового задания, существуют дополнительные задачи
* `показать знания и навыки владения OOP, ORM Doctrine 2 и Symfony 2 в целом` - от постановщика задания
* освежить в своей памяти особенности и навыки работы с Symfony фреймворком - для исполнителя задания.

Это и служит причиной появления следующего избыточного функционала:

#### Сервис AcmeNewsManager

Сервис, в котором инкапсулированы бизнес-правила обработки данных новостей при сохренении и извлечении их из БД
в различных случаях.

Использует NewsRepository для непосредственной манипуляции данными в БД,

Имел бы смысл в случае расширения функционала работы с данными новостей, например
* добавлении админки для управления новостями с разделением доступа к CRUD операциям по ролям пользователей
* встраивании какой-либо системы автоматического перевода (через внешнее АПИ) новостей на другие языки 
при публикации, т.п.

В текущих условиях сервис избыточен. Было бы достаточно собрать функционал манипуляции данными новостей в NewsRepository, 
сделав его сервисом.

#### Использование Memcached

и декоратор AcmeNewsManagerDecorator.

Имели бы смысл в случае большого количества посетителей сайта,
наряду с другими мерами по оптимизации приложения под высокие нагрузки.

#### Отдельный раздел конфигурации для бандла

Имеет смысл в слуачае выделения бандла в отдельный самостоятельный
внешний проект, который загружался бы в приложение composer'ом и 
распостранялся публично.

В текущих условиях для нужд бандла достаточно было бы использования параметров
в разделе parameters.

###Недостающий функционал
* Нет функциональных тестов