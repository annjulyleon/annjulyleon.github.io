---
title: Onlyoffice - установка на Linux
toc: true
toc_label: "Содержание"
tags:
  - onlyoffice
  - ru
categories:
  - guides
---

[ONLYOFFICE](https://www.onlyoffice.com/ru/) - отличное решения для организации совместной работы в небольшой организации. ONLYOFFICE имеет полный набор нужных приложений - почта, календарь, склад документов, облачный офис (редактирование онлайн), чат, форум и прочие плюшки (см. официальный [пи ар](https://www.onlyoffice.com/ru/server-solutions.aspx)). Ниже описана установка версии Community Edition с ОС Linux.

# Onlyoffice - установка на Linux

**ПО**: ONLYOFFICE  
**Сайт**: [onlyoffice.com](https://www.onlyoffice.com/ru/download.aspx) 
**Версия**: Community  

**TODO**:

1. Установить сервер ONLYEOFFICE (сервер совместной работы, сервер документов).
2. Перевести на HTTPS.
3. Подключить сервер документов к серверу совместной работы.

Основные отличия **Community** версии от **Enterprise**:

1. В Enterprise версии присутствует дополнительный модуль Control Panel, которые позволяет настраивать интеграцию с AD/LDAP, переводить на HTTPS и другие плюшки одним-двумя кликами.
2. Отдельные ограничения, которые можно посмотреть по документации (отмечены звездочкой), например при редактировании документов в Community Edition отсутствует возможность чата и комментариев.

ONLYOFFICE состоит из двух (трех -- в Enterprise Edition) основных компонентов: сервер совместной работы (сам сайт) и сервер документов (сервис, предоставляющий возможность редактировать документы онлайн). Компоненты можно поставить на один сервер (только в установке docker) или на разные (обязательно на разные в версии Linux).

## Установка версии-docker с помощью скрипта

**Официальная документация**: [здесь](http://helpcenter.onlyoffice.com/ru/server/docker/opensource/opensource-script-installation.aspx)

Эта версия Docker ONLYOFFICE содержит все компоненты и ставится на один сервер. Установка выполняется одним единственным скриптом.

1. Скачать скрипт: `wget http://download.onlyoffice.com/install/opensource-install.sh`
2. Запустить скрипт (установка без почты): `bash opensource-install.sh -ims false`

Все, сервер совместной работы готов и доступен по `http;//адрес-сервера`. Теперь, чтобы перевести сервер на HTTPS, например, с самоподписанным сертификатом:

Сгенерировать сертификаты (имя строго onlyoffice):

```bash
openssl genrsa -out onlyoffice.key 2048
openssl req -new -key onlyoffice.key -out onlyoffice.csr
openssl x509 -req -days 365 -in onlyoffice.csr -signkey
onlyoffice.key -out onlyoffice.crt
openssl dhparam -out dhparam.pem 2048
```

Положить файлы key, crt и .pem в папку `/app/onlyoffice/CommunityServer/data/certs`.

Перезапустить контейнер (названия контейнеров по умолчанию: onlyoffice-community-server, onlyoffice-document-server, onlyoffice-mail-server):
`docker restart onlyoffice-community-server`

Вроде бы все замечательно, но к несчастью версия docker работает куда медленнее, чем раздельная установка. Docker сильно нагружает процессор, но это не помогает ему быстрее грузить страницы, так что версию docker я заботливо забэкапила и упаковала: вдруг пригодиться, -- и перешла к установке Linux-версии onlyoffice.

## Установка сервера совместной работы (community-server)

**Официальная документация**: [onlyoffice.com](http://helpcenter.onlyoffice.com/ru/server/linux/community/linux-installation.aspx)  
**ОС**: Debian 8, обновленная  

Выполнить команды и добавить репозитории:

```bash
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
echo "deb http://download.mono-project.com/repo/debian wheezy main" | tee /etc/apt/sources.list.d/mono-xamarin.list
```

Для Debian:

```bash
echo "deb http://download.mono-project.com/repo/debian wheezy-libjpeg62-compat main" | tee -a /etc/apt/sources.list.d/mono-xamarin.list
echo "deb http://download.onlyoffice.com/repo/debian squeeze main" | tee /etc/apt/sources.list.d/onlyoffice.list
apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 8320CA65CB2DE8E5
apt-get update
apt-get install onlyoffice-communityserver
```

После установки сервер будет доступен по `http://ip-or-DNS/`.

После этого необходимо перевести сервер на https. Для этого отредактируем файл `/etc/nginx/sites-available/onlyoffice`. Необходимо заменить его содержимое на конфигурацию [по ссылке](https://github.com/ONLYOFFICE/Docker-CommunityServer/blob/master/config/nginx/onlyoffice-ssl). В конфигурации по ссылке явно видны следы версии на docker, поэтому необходимо некоторые параметры поправить:

- на строке 4 (`keepalive`) указать нужный параметр. Я указала `32`;
- в строке `DOCKER_ONLYOFFICE_SUBNET` указать локальную подсеть, по которой компоненты onlyoffice общаются между собой (например, 192.168.98.1 0);
- строка 49 (параметр `proxy_ssl_verify off;`) может в зависимости от версии nginx в сборке onlyoffice вызывать ошибку. Если у вас есть ошибка (journalctl -xn) по этому параметру, то необходимо его закомментировать;
- в строках `ssl_certificate, ssl_certificate_key, ssl_verify_client, ssl_client_certificate` указать пути к сертификатам;
- в сроке 78 (`add_header Strict-Transport-Security max-age;`) указать нужно значение, например 31536000;
- в строке `ssl_dhparam` указать путь к файлу dhparam;
- у меня nginx при попытке запуска ругался на неверно указанные типы, поэтому в строке `gzip_types` я убрала text/html (он и так всегда обрабатывается по умолчанию), получилось -  `application/x-javascript text/css application/xml`;
- в значении `DOCUMENT_SERVER_HOST_ADDR` указать адрес сервера документов, например `http://docserver.domain.ru`. Сервер должен быть доступен, иначе nginx не запустится;
- в значении  `CONTROL_PANEL_HOST_ADDR` указывается адрес панели управления, которая доступна только в платной версии, поэтому там ставим `http://localhost`.

Для применения изменения необходимо перезапустить веб-сервер:

```bash
sudo service nginx restart
```

Сервер не запустится, если адрес, указанные в строке сервера документов не доступен.

## Установка сервера документов (document-server)

**Официальная документация**: [здесь](http://helpcenter.onlyoffice.com/ru/server/linux/document/linux-installation.aspx)  
**ОС**: Debian 8  

Запускать установочные скрипт из под root или установить утилиты sudo и curl (актуально для Debian). Команды:

```bash
echo "deb http://archive.ubuntu.com/ubuntu precise main universe multiverse" | tee -a /etc/apt/sources.list
curl -sL https://deb.nodesource.com/setup_6.x | bash –
apt-get install postgresql
```

Создать базу:

```bash
sudo -u postgres psql -c "CREATE DATABASE onlyoffice;"
sudo -u postgres psql -c "CREATE USER onlyoffice WITH password 'onlyoffice';"
sudo -u postgres psql -c "GRANT ALL privileges ON DATABASE onlyoffice TO onlyoffice;"
```

(или `su - postgres psql -c "CREATE DATABASE onlyoffice;"` и т.д.).

Далее:

```bash
apt-get install rabbitmq-server
apd-get install update
apt-get install redis-server
apt-get install nodejs
apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys CB2DE8E5
apt-get update

echo "deb http://download.onlyoffice.com/repo/debian squeeze main" | tee /etc/apt/sources.list.d/onlyoffice.list

apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 40976EAF437D05B5

apt-get update

apt-get install onlyoffice-documentserver
```

Для того чтобы перевести сервер документов на HTTPS, необходимо заменить содержимое файла `/etc/nginx/conf.d/onlyoffice-documentserver.conf` на указанное по [ссылке](https://github.com/ONLYOFFICE/Docker-DocumentServer/blob/master/config/nginx/onlyoffice-documentserver-ssl.conf). В файле просто нужно указать пути к файлам сертификатов и dhparam.

После этого перезапустить nginx и перезапустить nginx на сервере совместной работы:

```bash
sudo service ngins restart
```

Проблемы: *После перевода серверов на https не открывается редактирование документов с ошибкой «ONLYOFFICE Unavailable Попробуйте позже…»*.

Если оба сервера (сервер совместной работы и сервер документов) работают с самозаверенными сертификатами, то сначала нужно открыть сервер документов в браузере и принять предупреждение безопасности, а затем уже открывать редактирование документов. Эта ошибка не возникает, если сертификат выдан удостоверяющим центром сертификации (например, letsencrypt).

_Редактирование документов недоступно, висит бесконечное «Подключение…»_

В консоли браузера (**F12** > Консоль) можно видеть ошибки доступа к файлу AllFonts. Эта ошибка может возникнуть, когда неправильно выполнен скрипт установки шрифтов. В этом случае скрипт нужно запустить повторно из папки `/usr/bin`:

```bash
cd /usr/bin
documentserver-generate-allfonts.sh
```

И перезапустить nginx: `sudo service nginx restart`

## Подключение сервера документов к серверу совместной работы

**Официальная документация**: [здесь](http://helpcenter.onlyoffice.com/ru/server/linux/document/connect-document-server.aspx).

Оба сервера должны быть доступны по публичному адресу. В статье по ссылке выше приведена схема, согласно которой серверы общаются между собой. По публичному адресу должен быть доступен API сервера документов (ссылка вида `https://docserver.domain.ru/OfficeWeb/apps/api/documents/api.js`).

В новых версиях сервера совместной работы (после 8.1) подключение выполняется через веб-интерфейс ONLYOFFICE на станице настроек *Интеграция > Служба документов*.  
В новых версиях сервера совместной работы полей будет больше.

Если после редактирования настроек на портале что-то не работает, то необходимо дополнительно прописать адреса в файле `/var/www/onlyoffice/WebStudio/web.appsettings.config` в соответствующих ключах `files.docservice.convert-docs`, `files.docservice.url.api` и т.д.

После установки необходимо проверить, что корректно работают почтовые оповещения.

Для этого на странице настроек *Интеграция > Настройки SMTP* нужно указать параметры вашего почтового сервера и открыть при необходимости нужные порты. Для тестирования настроек нажать кнопку **Тестировать** и проверить почту (даже если настройки работают, сервер на https может показать предупреждение, которое никак не влияет на работу оповещений. Происходит только на версии https).

Для проброса серверов наружу я использовала прокси-сервер (nginx reverse proxy), на который уже можно поставить валидные красивые зелененькие SSL сертификаты от letsencrypt.
