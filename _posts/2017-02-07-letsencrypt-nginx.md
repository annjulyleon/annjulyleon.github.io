---
title: Letsencrypt. Установка на nginx
toc: true
toc_label: "Содержание"
tags:
  - web
  - ru
categories:
  - guides
---

Рано или поздно это случается: количество сайтов, корпоративных сервисов, порталов растет, а внешние ip-шники заканчиваются. И тогда на помощь приходит он - прокси-сервер. Прокси-сервер по имени домена определяет сайт (сервер) внутри локальной сети, к которому требуется перенаправить трафик с одного единственного внешнего ip. На прокси также удобно управлять сертификатами для всех локальных сайтов.

**ОС**: Ubuntu 16.04 LTS  

**UPDATED**: теперь все это гораздо проще с [certbot](https://certbot.eff.org/) и [Nginx Proxy Manager](https://nginxproxymanager.com/).

Для организации проксирующего сервера использовать любой веб-сервер или прокси, например **Apache**, **Nginx** или **HAProxy**. Зачастую их используют совместно, например HAProxy в tcp режиме для балансировки нагрузки между несколькими Nginx. Мне пока достаточно **Nginx** для проксирования веб-сайтов, и **HAProxy** для проброса отдельных портов (например, для Jabber).

**TO DO**:
1. установить nginx;
2. создать виртуальные хосты для проксирования;
3. установить сертификаты letsencrypt.

Установка выполняется стандартной командой, по умолчанию ставятся все необходимые для обычной работы модули:
```bash
sudo apt-get install nginx -y
```
Для проксирования высоконагруженных сайтов вероятно потребуется специальная сборка с каким-нибудь mod_pagespeed (для ускорения загрузки), а возможно даже и memcached.

# Настройка и генерация сертификата letsencrypt

Источники: [serverfault.com](http://serverfault.com/questions/768509/lets-encrypt-with-an-nginx-reverse-proxy), [официальная дока letsencrypt](https://certbot.eff.org/#ubuntuxenial-nginx), [фокусы из блога dbain](http://blog.dbain.com/2016/08/installing-ssl-certificates-with-nginxs.html)

Бесплатный центр сертификации Letsencrypt выдает сертификаты на 90 дней и поддерживает автоматическое обновление.

1. Создать директорию  `/var/www/имяДомена`, например `sudo mkdir /var/www/mysite.domain.ru`. Эта директория нужна для того, чтобы letsencrypt мог проверить сервер, загрузив туда свои файлы.
2. Создать конфигурационный файл сайта в папке `/etc/nginx/sites-available`: `sudo nano /etc/nginx/sites-available/mysite.domain.ru`
3. В конфигурационном указать базовые настройки прокси-сервера:

```
*#внутренний ip-адрес сервера, на который будет перенаправляться трафик с внешнего ip;*
upstream mysite.domain.ru {
   server 192.168.98.63;
}
server {
   listen 80;
   #Доменные имена сайта (как указано в DNS-зоне провайдера) 
   server_name mysite.domain.ru;
   #Ссылка на директорию, созданную на 1 шаге. Эта директория будет открыта с прокси сервера, и не будет проксироваться на upstream сервер

location /.well-known {
   alias /var/www/mysite.domain.ru/.well-known;
 }
location / {
   proxy_pass https://192.168.98.63;
   proxy_set_header Host $host;
   proxy_set_header X-Real-IP $remote_addr;
   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_set_header X-Forwarded-Proto $scheme;
   }
}
```
Активировать сайт, создав ссылку на конфигурационный файл в `/etc/nginx/sites-enabled` и перезапустить nginx:

```bash
sudo ln -s /etc/nginx/sites-available/mysite.domain.ru /etc/nginx/sites-enabled/mysite.domain.ru
sudo service nginx restart
```
Установить клиент letsencrypt. На сайте [certbot](https://certbot.eff.org/) можно выбрать операционную систему сервера и ПО веб-сервера, для которых будет отображена инструкция по установке.

```bash
 sudo apt-get install letsencrypt
```

Хотя летом 2016 года официальный клиент letsencrypt был переименован в certbot (для ОС Debian он будет называться именно так), но в репозиториях для ubuntu 16.04 клиент последней версии называется letsencrypt.

Выполнить команду вида:
```bash
sudo letsencrypt certonly --webroot -w /var/www/mysite.domain.ru -d mysite.domain.ru
```
где в ключах `-w` - указать путь к директории, созданной на шаге 1, в ключах `-d` - домены, на которые требуется выдать сертификат (можно указать несколько, например `-d mysite.domain.ru -d www.mysite.domain.ru`).

Для того, что *letsencrypt* мог выполнить проверки для выдачи сертификата, доменное имя должно быть зарегистрировано на внешней DNS зоне провайдера. Letsencrypt прежде всего проверяет авторизованные сервера ([ссылка](https://community.letsencrypt.org/t/i-have-to-wait-until-dns-propagate-for-issuing-certificate/18573)), поэтому запрос сертификата можно выполнять в течение 5 мин после редактирования внешней зоны. Кстати, проверить обновление DNS-зон можно с помощью сайта [https://www.whatsmydns.net ](https://www.whatsmydns.net/).

В интерактивном окне ввести почту (желательно - существующую, так как она будет использоваться для восстановления доступа к утерянным сертификатам), нажать Agree.

Сертификат и ключи будут созданы в папке `/etc/letsencrypt/live/mysite.domain.ru/` . Информация об аккаунте сохранена в `/etc/letsecnrypt`.
Сгенерить dhparam для сайта, если еще не сгенерирован. Я складываю их в отдельные папки для каждого сайта, чтобы не путаться:
```bash
cd /etc/nginx/ssl/mysite.domain.ru
sudo openssl dhparam -out dhparam.pem 2048
```
Теперь переключим сайт на https. Для этого редактируем конфигурацию `/etc/nginx/sites-available/mysite.domain.ru`

```
upstream mysite.domain.ru {
  server 192.168.98.63;
}
#В блоке ниже - правило перенаправления с http на https, так что все запросы будут направляться на https
server {
  listen 80;
  server_name mysite.domain.ru;
  return 301 https://$host$request_uri;
}

server {
  listen 443 ssl http2;
  server_name mysite.domain.ru;
  ssl_certificate /etc/letsencrypt/live/mysite.domain.ru/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/mysite.domain.ru/privkey.pem;
  ssl_trusted_certificate /etc/letsencrypt/live/mysite.domain.ru/chain.pem;
  ssl_dhparam /etc/nginx/ssl/mysite.domain.ru/dhparam.pem;
 
 \#Настройки ssl ниже оставляю без изменений для большинства сайтов
  ssl_stapling on;
  ssl_stapling_verify on;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
  ssl_prefer_server_ciphers On;        
  
  \#Параметр ниже необходимо раскомментировать после проверки работоспособности сайта
  \#add_header Strict-Transport-Security "max-age=31536000";
  
  \#Для обновления сертификатов 
  location /.well-known {
    alias /var/www/mysite.domain.ru/.well-known;
  }

  location / {
  proxy_pass https://192.168.98.63;
  proxy_set_header Host $host;
  proxy_set_header X-Real-IP $remote_addr;
  proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  proxy_set_header X-Forwarded-Proto $scheme;
  
  \#В этот файл складываю общие настройки прокси (см ниже)
  include /etc/nginx/sites-available/proxy.cfg;
   }
}
```

Файл `proxy.cfg` из конфигурации выше:

```
proxy_buffers 32 4m;
proxy_busy_buffers_size 25m;
proxy_buffer_size 512k;
proxy_ignore_headers "Cache-Control" "Expires";
proxy_max_temp_file_size 0;
client_max_body_size 1024m;
client_body_buffer_size 4m;
proxy_connect_timeout 300;
proxy_read_timeout 300;
proxy_send_timeout 300;
proxy_intercept_errors off;
```


Перезапустить nginx для принятия изменений: `sudo service nginx restart`.

Если все работает - раскомментировать строку в конфигурации: `add_header Strict-Transport-Security max-age=31536000` - и еще раз перезапустить nginx.

# Заблокировать доступ по внешнему IP

Чтобы снаружи нельзя было получить доступ к сайту по IP необходимо:
Удалить `default` сайт nginx:
```bash
sudo rm -v /etc/nginx/sites-enable/default
sudo service nginx restart
```
В конфигурацию сайта (достаточно добавить в конфигурацию первого сайта по алфавиту) добавить блок в начало файла (сразу после блока upstream):
```
server {    
 return 444;
}
```

Это заблокирует доступ по http, но если добавить https перед ip - доступ останется. Если попробовать заблокировать также https - перестают работать корректно https сайты, поэтому:
В блок сервера https добавить условие сразу после строки с `server_name`:

```
server_name example.com
if ($host != "example.com") {
 return 444;
}
```

и перезапустить nginx.

# Обновление сертификата letsencrypt

Тестовый запуск обновления (не генерирует сертификаты):
```
sudo letsencrypt renew --dry-run --agree-tos
```

Предупреждение warning про email можно игнорировать, это баг ubuntu 16.04

Команда обновления всех сертификатов, установленных на сервере:
```
letsencrypt renew
```
Если сертификат не требует обновления, то он не будет обновлен.

# Удаление сертификата letsencrypt

Предположим сайт вам больше (или пока) не нужен, а на прокси сертификат на домен пытается обновляться и пишет кучу ошибок. Как удалить сертификат из обновления?

Встроенной команды letsencrypt для этого нет (хотя [планируется](https://www.directadmin.com/features.php?id=1860)).

Чтобы просто исключить сайт из автообновления, необходимо удалить (сделав предварительно копию) файл `/etc/letsencrypt/renewal/mysite.domain.ru.conf`.  Кстати, в том же файле можно отредактировать домены, на которые выдаются сертификаты.

Проверить, что сертификат не будет обновляться можно запустив тестовое обновление:

```
sudo letsencrypt renew --dry-run --agree-tos
```

