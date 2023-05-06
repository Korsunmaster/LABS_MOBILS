# LABS_MOBILS
## Лабораторная работа № 2
В данной лабораторной работе мы должны познакомиться с основными командами Docker.
Для выполнения этой работы я буду использовать сайт https://labs.play-with-docker.com/

### Пройдемся по практической части:
Для создания и запуска контейнера запустим команду `docker container run nginx`. 
Для запуска контейнера необходим образ, а его у нас не было, поэтому с помощью этой команды загрузился также и образ **nginx** из dockerhub, что видно из командной строки ниже.
```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
26c5c85e47da: Pull complete 
4f3256bdf66b: Pull complete 
2019c71d5655: Pull complete 
8c767bdbc9ae: Pull complete 
78e14bb05fd3: Pull complete 
75576236abf5: Pull complete 
Digest: sha256:b1d73114312c09bdfea340c6debddd3230951d3b2a4cf8a8deb2e9326f135314
Status: Downloaded newer image for nginx:latest
```
После этого посмотрим на наши образы, для этого воспользуемся командой `docker images`.

Увидим следущее:
```bash
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
nginx        latest    448a08f1d2f9   15 hours ago   142MB
```
У нас создался образ **nginx**, его размер составляет 142mb.

Далее запустим еще один контейнер, но уже в фоновом режиме с помощью команды `docker container run -d nginx`. 

Флаг `-d` означает сокращение от полной команды `--detach`.

Теперь посмотрим на наши контейнеры, для этого используем команду `docker ps -a`. Флаг `-a` в свою очередь означает сокращение от `--all`:
```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                     PORTS     NAMES
b8dca6122448   nginx     "/docker-entrypoint.…"   11 seconds ago   Up 10 seconds              80/tcp    strange_feynman
8d1a0b9e6951   nginx     "/docker-entrypoint.…"   6 minutes ago    Exited (0) 3 minutes ago             romantic_bardeen
```
Из колонки **STATUS** мы можем увидеть, что один контейнер работает, а другой же приостановлен.

Давайте же запустим его с помощью команды `docker start`, а после нее необходимо указать **NAMES** или **CONTAINER ID**

Мы применим команду попроще и запустим с помощью имени: `docker start romantic_bardeen` и проверим результат с помощью команды для просмотра активных контейнеров `docker ps`:
```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
b8dca6122448   nginx     "/docker-entrypoint.…"   12 minutes ago   Up 12 minutes   80/tcp    strange_feynman
8d1a0b9e6951   nginx     "/docker-entrypoint.…"   18 minutes ago   Up 3 seconds    80/tcp    romantic_bardeen
```
Ура, теперь наш контейнер имеет статус **Up**, т.е. активен.

В активном состоянии удалить контейнер не получится, поэтому для начала нужно приостановить их работу(с помощью команды `docker stop`), а затем удалить. Но есть один флаг, который нам поможет не прибегать к остановки и удалить даже запущенный контейнер. 

Флаг `-f`, а если полностью `--force` c громким переводом **сила**. Т.е. мы насильно удалим этот запущенный контейнер. Для этого используем `docker rm strange_feynman romantic_bardeen -f` и уже прописав команду `docker ps -a` увидим, что не осталось ни одного контейнера:
```bash
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
Далее мы можем удалить образ с помощью команды `docker rmi nginx`, *Но убедитесь, что у вас нет запущенных контейнеров иначе терминал может выдать ошибку*. Поэтому удалите контейнеры перед удалением образа, либо используйте волшебный флаг `-f` в команде `docker rmi nginx -f`

Видим, что терминал удалил наш образ:
```bash
Untagged: nginx:latest
Untagged: nginx@sha256:480868e8c8c797794257e2abd88d0f9a8809b2fe956cbfbc05dcc0bca1f7cd43
Deleted: sha256:448a08f1d2f94e8db6db9286fd77a3a4f3712786583720a12f1648abb8cace25
Deleted: sha256:6b33c8bf5207fd88b6e0f942c230c59477990205dbed0ae41d54b5b29ed1051d
Deleted: sha256:a673eda43a02c5a8218e8be171c43912dc9646d588a881a463be970b7f06abf0
Deleted: sha256:e22652bd991fd7a83155d12651d319458cb233d428ca769323ecb0b1d6549844
Deleted: sha256:77350fbf9b519374ed1eee1c2387b1c9af0c7f048d11794fe172006323834954
Deleted: sha256:556cbc099a5c304d0f2fed44d6d153b7d74be08fce2b4ffe74b1183b75c5cae6
Deleted: sha256:8553b91047dad45bedc292812586f1621e0a464a09a7a7c2ce6ac5f8ba2535d7
```
И при запуске команды `docker images` или `docker image ls` мы видим, что список пуст:
```bash
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

Теперь же создадим и запустим контейнер в фоновом режиме на образе "nginx" и зададим ему **порт** на **хост** машине. Для этого воспользуемся командой

`docker container run -d -p 80:80 --name proxy nginx` или `docker container run --detach --publish 80:80 --name proxy nginx` с полной расшифровкой флагов.

Увидим:
```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
9e3ea8720c6d: Pull complete 
bf36b6466679: Pull complete 
15a97cf85bb8: Pull complete 
9c2d6be5a61d: Pull complete 
6b7e4a5c7c7a: Pull complete 
8db4caa19df8: Pull complete 
Digest: sha256:480868e8c8c797794257e2abd88d0f9a8809b2fe956cbfbc05dcc0bca1f7cd43
Status: Downloaded newer image for nginx:latest
29c51c3b751b27f6b23f229e5b151e391d368623b02e949b39fcb3f40a926a93
```
Необходимо помнить, что **NAME** является индивидуальным значением, также как и **PORT** поэтому нельзя создавать контйенеры с одинаковыми именами или на одном и том же порту хост машины.

Для ситуации, когда контейнер может выключиться(*важно: выключен не вручную командой* `docker stop`), чтобы контейнер автоматически перезапускался можем воспользоваться `docker container run -d -p 80:80 --name proxy --restart always nginx`.

1. no - стоит по умолчанию.
2. on-failure - перезагрузить в случае ошибки.
3. always - всегда перезагружать.
4. unless-stopped - пока контейнер не будет остановлен.

В этой же команде `docker container run --rm -d -p 80:80 --name proxy nginx` присутствует флаг `--rm` - он означает, что контейнер будет удален при остановке:
```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                NAMES
3dc5b1768e8b   nginx     "/docker-entrypoint.…"   5 seconds ago   Up 4 seconds   0.0.0.0:80->80/tcp   proxy
```
Пропишем команду docker `docker stop proxy` и проверим командой `docker ps -a`
```bash
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
Как мы можем убедиться, контейнер был удален после остановки.

Заного создадим контейнер `docker container run --rm -d -p 80:80 --name proxy nginx`.
Для того, чтобы посмотреть метаданные контейнера можем использовать `docker inspect proxy`, а чтобы отформатировать и выбрать именно то, что нам необходимо воспользуемся командой `docker inspect proxy --format "IP: {{ .NetworkSettings.IPAddress }} | Gateway: {{.NetworkSettings.Networks.bridge.Gateway}}"`

Для получения **IP** и **Gateway** шлюза соответственно.Тогда получим:
```bash
IP: 172.17.0.2 | Gateway: 172.17.0.1
```
Для получение **ID** и **PORTS** возьмем команду `docker container inspect proxy --format "ID: {{.Id}} | Port: {{.NetworkSettings.Ports}}"`
```bash
ID: d570ab1826dfb23d001be461835d775d5fc2d5d36c8e6cd11f5ec90b66d95a2c | Port: map[80/tcp:[map[HostIp:0.0.0.0 HostPort:80]]]
```
Проверим, перейдя на локальный хост и затем пропишем команду `docker container logs proxy`
```bash
172.18.0.1 - - [06/May/2023:13:57:42 +0000] "GET / HTTP/1.1" 200 615 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/112.0" "-"
2023/05/06 13:57:42 [error] 35#35: *2 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.18.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "ip172-18-0-88-chb5fvg1k7jg00dq26t0-80.direct.labs.play-with-docker.com", referrer: "http://ip172-18-0-88-chb5fvg1k7jg00dq26t0-80.direct.labs.play-with-docker.com/"
172.18.0.1 - - [06/May/2023:13:57:42 +0000] "GET /favicon.ico HTTP/1.1" 404 153 "http://ip172-18-0-88-chb5fvg1k7jg00dq26t0-80.direct.labs.play-with-docker.com/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/112.0" "-"
```
Действительно логи передались на наш веб-сервер.

Чтобы быстро переименовать контейнер прописываем `docker container rename proxy proxy_new`, где *proxy_new* новое имя соответственно.

Для обновления индексов пакетов в образе и обновления самих пакетов воспользуемся `docker container exec -it proxy bash` и сразу же `apt update && apt upgrade`, а в конце `exit` для выхода.

Увидим, что размер нашего контейнера также изменился `docker ps -as`
```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                NAMES     SIZE
d570ab1826df   nginx     "/docker-entrypoint.…"   22 minutes ago   Up 22 minutes   0.0.0.0:80->80/tcp   proxy     18MB (virtual 160MB)
```
Добавим другой образ командой `docker image pull alpine:3.7` - **3.7** после двоеточия является тегом образа. если же его не указывать, то загрузится последняя версия.
```bash
3.7: Pulling from library/alpine
5d20c808ce19: Pull complete 
Digest: sha256:8421d9a84432575381bfabd248f1eb56f3aa21d9d7cd2511583c68c9b7511d10
Status: Downloaded newer image for alpine:3.7
docker.io/library/alpine:3.7
```
И теперь мы можем создать контейнер уже на образе **alpine**. Для этого можно использовать одну из самых простых команд `docker container run alpine:3.7`
```bash
CONTAINER ID   IMAGE        COMMAND     CREATED              STATUS                          PORTS     NAMES
24ab1a045f0e   alpine:3.7   "/bin/sh"   About a minute ago   Exited (0) About a minute ago             thirsty_cartwright
```

Скачаем пользовательский образ `docker image pull fholzer/nginx-brotli`
```bash
latest: Pulling from fholzer/nginx-brotli
f56be85fc22e: Pull complete 
990168415dd8: Pull complete 
3d8e9eecae3d: Pull complete 
e6e686f3722e: Pull complete 
6b301e7eabd1: Pull complete 
074bb6825773: Pull complete 
56255d8e279a: Pull complete 
c0280a4b5eaf: Pull complete 
20d8f0436d02: Pull complete 
9beab895cf94: Pull complete 
Digest: sha256:74d73fc1e48b8f99fd73908b4da1edac432bee0a7091293bf2e43f9270434f39
Status: Downloaded newer image for fholzer/nginx-brotli:latest
docker.io/fholzer/nginx-brotli:latest
```
Изменим тег образа для подготовки к отправке в наш репозиторий: `docker image tag fholzer/nginx-brotli user3535/brotli`, где user3535/brotli - новое название.

Отправим данный образ на наш docker hub `docker image push user3535/brotli`, *не забудьте авторизоваться через команду* `docker login`
```bash
Using default tag: latest
The push refers to repository [docker.io/user3535/brotli]
faafbcb0a85d: Mounted from fholzer/nginx-brotli 
dbed8c82609d: Mounted from fholzer/nginx-brotli 
653cc7fef7d4: Mounted from fholzer/nginx-brotli 
45fcf1aff8d1: Mounted from fholzer/nginx-brotli 
0bcf6c8a48e3: Mounted from fholzer/nginx-brotli 
7c6db038b319: Mounted from fholzer/nginx-brotli 
2a7e3250de10: Mounted from fholzer/nginx-brotli 
3a255727babb: Mounted from fholzer/nginx-brotli 
76aad2304b29: Mounted from fholzer/nginx-brotli 
f1417ff83b31: Mounted from fholzer/nginx-brotli 
latest: digest: sha256:616f31489c1596537b7f510ac22c91607c13e213d1414a0d76e1dcac6f4f9699 size: 2404
```
Увидим, что наш образ загрузился в наш docker hub и все прошло успешно!
