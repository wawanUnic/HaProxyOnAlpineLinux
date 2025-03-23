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


1. Переводчим порт 443 на 444
2. Усатнавиваем хапрокси и добавляем в автозапуск
3. устанавливаетм цертбот
4. кофигурируем хапрокси
5. создаем первый сертификат для каждого домера
6. создаем повторный скрипт
7. настриваем крон

### 22. Для сохрания коммита в постоянную память Alpine используем команду
```
lbu commit
```

9. перезапускаем перепроверяем.
10. 
