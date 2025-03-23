# HaProxy on AlpineLinux

Процессор / память - VIA Eden Processor 1000MHz (32бит) / 0,5 Гб DDR2

Версия AlpineLinux / Версия ядра Linux - 3.21.3 / 6.12.13-0-lts

HAProxy version 3.0.9-7f0031e 2025/03/20

Настройка производится для доменов:
```
nero-dozzle.duckdns.org
nero-n8n.duckdns.org
nero-supabase.duckdns.org
nero-flowise.duckdns.org
```

Работаем от root

### 1. Впишем новые репозитории для обновления пакетов
```
vi /etc/apk/repositories
	I
	http://dl-cdn.alpinelinux.org/alpine/v3.21/main
	http://dl-cdn.alpinelinux.org/alpine/v3.21/community
	Esc
	:wq
 ```

### 2. Обновим систему
```
apk update
apk upgrade
```

### 3. Впишем доп.папку для коммитов (эта папка по-умолчанию не включена в список для коммита)
```
lbu include /etc/init.d/
```

### 4. Переводим системный сервис ACF с порта 443 на порт 444 (чтобы освободить порт 443 для HaProxy)
```
vi /etc/mini_httpd/mini_httpd.conf
	I
	port=444
	Esc
	:wq
rc-service mini_httpd restart
 ```

### 5. Устанавиваем HaProxy и добавляем автозагрузку
```
apk add haproxy
haproxy version
rc-update add haproxy default
rc-service haproxy start
rc-service haproxy status
```

### 6. Устанавливаем certBot
```
apk add certbot
certbot --version
```

### 7. Конфигурируем HaProxy
```
global
    maxconn 4096
defaults
    log global
    mode http
    timeout connect 5s
    timeout client 30s
    timeout server 30s
frontend http_front
    bind *:80
    acl letsencrypt-acl path_beg /.well-known/acme-challenge/
    redirect scheme https code 301 if !letsencrypt-acl
    use_backend letsencrypt-backend if letsencrypt-acl
frontend https_front
    bind *:443 ssl crt /etc/haproxy/certs/
    acl host_site1 hdr(host) -i nero-dozzle.duckdns.org
    acl host_site2 hdr(host) -i nero-supabase.duckdns.org
    acl host_site3 hdr(host) -i nero-n8n.duckdns.org
    acl host_site4 hdr(host) -i nero-flowise.duckdns.org
    use_backend dozzle if host_site1
    use_backend supabase if host_site2
    use_backend n8n if host_site3
    use_backend flowise if host_site4
    default_backend backend_default
backend letsencrypt-backend
    server certbot 127.0.0.1:1111
backend dozzle
    server server12:8088 192.168.4.117:8088 check
backend supabase
    server server12:8000 192.168.4.117:8000 check
backend n8n
    server server12:5678 192.168.4.117:5678 check
backend flowise
    server server12:3000 192.168.4.117:3000 check
backend backend_default
    errorfile 503 /etc/haproxy/errors/503.http
listen stats
    bind *:8404
    stats enable
    stats uri /stats
    stats realm HAProxy\ Statistics
    stats auth admin:password
    stats admin if TRUE
    stats refresh 60
```

### 8. Проверяем конфигурационный файл и перезапускаем HaProxy
```
haproxy -c -f /etc/haproxy/haproxy.cfg
rc-service haproxy reload
```

6. создаем первый сертификат для каждого домера
7. создаем повторный скрипт
8. настриваем крон

### 22. Для сохрания коммита в постоянную память Alpine используем команду
```
lbu commit
```

9. перезапускаем перепроверяем.
10. 
