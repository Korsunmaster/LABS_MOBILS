# LABS_MOBILS
## Лабораторная работа № 1
В данной лабораторной работе мы установили docker и подготовили его к работе.

Скрипт для установки на моей системе, а это **Debian GNU/Linux 11 (bullseye)** будет выглядеть следущим образом:
```bash
#!/bin/bash
sudo apt update -y
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install docker-ce -y
sudo systemctl start docker
sudo systemctl enable docker

sudo curl -L "https://github.com/docker/compose/releases/download/1.28.6/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo usermod -aG docker $USER
sudo gpasswd -a $USER docker
reboot
```
Для запуска рандомного контейнера на самом простом образе alpine без тегов(будет взят последний), воспользуемся командой `docker container run alpine`

Т.к. у нас не было данного образа, то в консоли мы можем увидеть его установку:
```bash
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
f56be85fc22e: Pull complete 
Digest: sha256:124c7d2707904eea7431fffe91522a01e5a861a624ee31d03372cc1d138a3126
Status: Downloaded newer image for alpine:latest
```
Посмотрев на него с помощью команды `docker ps -a` можем увидеть, что он создался, но не запущен:
```bash
CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS                     PORTS     NAMES
f13db965bcea   alpine    "/bin/sh"   9 minutes ago   Exited (0) 9 minutes ago             beautiful_liskov
```

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

## Лабораторная работа №3
Проходясь по практической части мы создали новые сети с разными типами и подключали к ним контейнеры. Добавляли копию образа в локальный репозиторий, а также ставили лимиты на использование ресурсов. Теперь остановим и удалим все контейнеры, кроме **portainer**.
```bash
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
a671c3e19bcff4f1c978e878a2e86503e36e6caf59025e76426d7fc5a85e35c1
c03b09ab034d241ac1d57c8ee798e0910f5e9b85440ec04dd12795c67034ba4d
dda89d4535c110dfd68e1741a06dd7fe11cc25d884852020cc5c8b5b09022a3f
477311c540ecb760b709e731ef98756b5df5505b68f4ee57864511f2e2fa4cfb
```
Удалим все неиспользуемые образы:
```bash
WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N] y
Deleted Images:
untagged: localhost:5000/nginx:v1
untagged: localhost:5000/nginx@sha256:3f01b0094e21f7d55b9eb7179d01c49fdf9c3e1e3419d315b81a9e0bae1b6a90
deleted: sha256:448a08f1d2f94e8db6db9286fd77a3a4f3712786583720a12f1648abb8cace25
deleted: sha256:6b33c8bf5207fd88b6e0f942c230c59477990205dbed0ae41d54b5b29ed1051d
deleted: sha256:a673eda43a02c5a8218e8be171c43912dc9646d588a881a463be970b7f06abf0
deleted: sha256:e22652bd991fd7a83155d12651d319458cb233d428ca769323ecb0b1d6549844
deleted: sha256:77350fbf9b519374ed1eee1c2387b1c9af0c7f048d11794fe172006323834954
deleted: sha256:556cbc099a5c304d0f2fed44d6d153b7d74be08fce2b4ffe74b1183b75c5cae6
deleted: sha256:8553b91047dad45bedc292812586f1621e0a464a09a7a7c2ce6ac5f8ba2535d7
untagged: registry:2
untagged: registry@sha256:8c51be2f669c82da8015017ff1eae5e5155fcf707ba914c5c7b798fbeb03b50c
deleted: sha256:8db46f9d755043e6c427912d5c36b4375d68d31ab46ef9782fef06bdee1ed2cd
deleted: sha256:77772f9a7bb9df7f2d25e374dcbda41b9025b4ce5a26f17d10043d39d9bcafac
deleted: sha256:e5d7cfb455bd379332a3da31db783040de85cae7e981f05ae1388e11ca8b0e46
deleted: sha256:b7109a1a0d45f2335e66af07f5161c4181565bf6b4bb7541e456ba15e1f62019
deleted: sha256:5e85a660413809aa0f42ad3ed494f7a3b40fdf5349d3a6c30cbd41b6dacdd10c
deleted: sha256:5bc340f6d4f5a3bc999dfbc790a0bdf0920b9103ef794645034de4260ee4e9c8
untagged: hyper/docker-registry-web:latest
untagged: hyper/docker-registry-web@sha256:723ffa29aed2c51417d8bd32ac93a1cd0e7ef857a0099c1e1d7593c09f7910ae
deleted: sha256:0db5683824d8669ef8494f6e2c3aebf29facbda82a07f17e76bc60e752287144
deleted: sha256:56ee246b17949bd09bdf503f9d202c4e7aed680797e6598901ddf87f46fd858e
deleted: sha256:3dead94b503fa848d4e08cecd05e37945dc5a6e2b7137b4de209e2b2cfc043db
deleted: sha256:910675af3eff9cf844722af8a009cedd566c2a44d732f955f7c9e7e9faaffa55
deleted: sha256:50e6a256d26f7b8e867b276984a14bd25d2e092cbc00ae2f5ccf1627458d8eb6
deleted: sha256:7b225bd351d89c4c0d28f26dbf7f38a7cfebed1d9b3fc7ddd04c72af333e3f07
deleted: sha256:fea490391063169e21a4fee84f7124396f835f0b291b49de7f56cb97283dfffb
deleted: sha256:3d03273e727f847c569640cd5da7acde57a8d1394822dc264276b8e17ffc1cad
deleted: sha256:d4c38f621039ee415159634420cd8389a4974848b7b2a57e7e68e2157a8e419f
deleted: sha256:a10185c8f44ff4c7c9edb8872ba6d5e8dd1315e35dcd1130a66605fd543469cb
deleted: sha256:6b7ddba845238a43511aac4e1ca2eeef83c2d2e08b877f3a5fbe18a4c0d176ad
deleted: sha256:3b3a42270c0ba8907019cd353db9f8668fea2c78a49d429fe539428a51085f00
deleted: sha256:131c7d0773244dee5ab433ebb9f4d549b5b0f1da993e267eb402b90df5fcc672
deleted: sha256:c4dc7097cf2fb3175acceb331facfbb08ed9ed825c545e5b40eb67c56c599e9e
deleted: sha256:2681c27be3d143a3055c763841d77205c82c93659145b993aca94e0af2a8d437
deleted: sha256:bac54b1b7ba32f79b3281f8349f03b080472233c69824629ced0869bdfca5691
deleted: sha256:f9cf4c588d1eccf2f7156b5dd53602fc7d7b4e734aa146a65259109b8924b495
deleted: sha256:0e960c44ab71e08aa13344394051499d4c58fec66431f0852b7e9410488c548a
deleted: sha256:294abf6a4e126ba047fd329c52e218ffa5f98a9dd7946aeb058ffaae1be36b81
deleted: sha256:092873c8fc45dc02e52cc30affd25114e667e2215580ff53944c48e93990434c
deleted: sha256:3cd07b51c52cb173d919276baeffb83d350887240e716528119da411fd73ba47
deleted: sha256:78ff13900d615402568a24175b5ff1d7582c12f3ba4903f99a73752c6d5f597e

Total reclaimed space: 765MB
```
Удалим все тома:
```bash
6107ad83cd5b7a89aae2a08907132a72716c724cb494cf4259f0217a0b26c658
d6536aa20990f34ef5066aa13bbb065a43afbd53141ace88b1ac2436713fc41e
Error response from daemon: remove 08379a7b46a43e3fdf2f86cab2bc317d165b8a898570e02190ce4f9a29496602: volume is in use - [1ebb1435c9ddbd64192bcababf00a55d5895e8fcd1c5734e11b0fe9c6544b98b]
```
> Ошибка находящаяся выше показывает, что один том она не может удалить, ведь на нем присутствует наш контейнер **portainer**

И это мы можем проверить командой `docker ps -a --filter volume=08379a7b46a43e3fdf2f86cab2bc317d165b8a898570e02190ce4f9a29496602`, где после **volume=** написано название тома.

Далее установим контейнер от *jc21/registry-ui*. Используя `docker run jc21/registry-ui`
```bash
Unable to find image 'jc21/registry-ui:latest' locally
latest: Pulling from jc21/registry-ui
27833a3ba0a5: Pull complete 
63859d5abfc2: Pull complete 
8f1f0b7d83c3: Pull complete 
3465a4b08904: Pull complete 
760694bcb4d0: Pull complete 
ceb1125e8429: Pull complete 
Digest: sha256:98461dece795745f8822afc8657a7bd8ee97223f3b7fff75ebe56a2ec4051a3b
Status: Downloaded newer image for jc21/registry-ui:latest
```
Если же мы захотим воспользоваться API docker, то можем подключить **dockerUI**. Используем команду для быстрого запуска с официального *git*: `docker run -d -p 9000:9000 --privileged -v /var/run/docker.sock:/var/run/docker.sock uifd/ui-for-docker`
```bash
Unable to find image 'uifd/ui-for-docker:latest' locally
latest: Pulling from uifd/ui-for-docker
841194d080c8: Pull complete 
Digest: sha256:fe371ff5a69549269b24073a5ab1244dd4c0b834cbadf244870572150b1cb749
Status: Downloaded newer image for uifd/ui-for-docker:latest
afca67a07e4de733cf28be0938030bcfa1cba27b29db70204f3917336136a3a6
```
Мы подключили docker к API и перейдя по по локальному адресу с портом *9000* мы можем попасть на сайт для управления контейнерами http://localhost:9000

И в конце создадим контейнер **apache**, установим на него лимит по оперативной памяти и процессору: `docker run -dit -m 50m --cpus 1 --name limit -p 8080:80 httpd:2.4`
```bash
Unable to find image 'httpd:2.4' locally
2.4: Pulling from library/httpd
9e3ea8720c6d: Pull complete 
35c516cd98eb: Pull complete 
3050fa5900bc: Pull complete 
f14b7012c455: Pull complete 
014a91cb6174: Pull complete 
Digest: sha256:90254ccc7352e1a5c8d1e4cdab2a032cefac9fd5d4d632ca003a2943c9a9b0a3
Status: Downloaded newer image for httpd:2.4
e56d7a35bbd9f535bf5f97fee14ef7eef5f1ea9507d3b452719bc146a0ab1c00
```
```bash
CONTAINER ID   IMAGE                COMMAND              CREATED          STATUS          PORTS                                       NAMES
e56d7a35bbd9   httpd:2.4            "httpd-foreground"   10 seconds ago   Up 3 seconds    0.0.0.0:8080->80/tcp, :::8080->80/tcp       limit
```
```bash
CONTAINER ID   NAME            CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O    PIDS
e56d7a35bbd9   limit           0.00%     26.23MiB / 50MiB      52.47%    4.89kB / 1.18kB   0B / 4.1kB   82
```
Из сообщений терминала выше можем понять,что контейнер хорошо работает с заданными лимитами, также мы можем это проверить перейдя по локальному адресу с установленным нами портом *8080*: http://localhost:8080

## Лабораторная работа № 4

В этой лабораторной работе мы создадим самый простой одностраничный сайт на **php** с выводом 1 строки и подключим к нему **Dockerfile**, а после запустим на 80 порту локальной машины.

Создаем в новой папке 2 файла: *Dockerfile* и index.php
```bash
FROM php:8.2-apache
COPY . /var/www/html
WORKDIR /var/www/html
EXPOSE 80
```

```bash
<?php
    echo "Hello Anton Dmitrievich";
```
Теперь создадим образ из нашего образа Dockerfile и назовем его *myphp*, к примеру.

`docker build . -t myphp`

После сборки мы можем запстить наш контейнер назначив ему 80 порт на локальной машине, к примеру, и 80 порт на хост машине, т.к. он у нас указан в Dockerfile.

`docker run -d -p 80:80 myphp`

И теперь перейдя по локальному ip мы можем увидеть наш самый простой сайт http://localhost:80

Также мы можем выгрузить наш проект с докерфайлом и сайтом на наш репозиторий.

Для этого перейдем в нашу папку с помощью команды `cd` и далее путь к созданной папке.

Там пропишем `git init` затем `git add .` далее `git commit -m 'php'` еще `git remote add origin git@github.com:Korsunmaster/LABS_MOBILS.git` и наконец `git push --set-upstream origin master`

```bash
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
Перечисление объектов: 4, готово.
Подсчет объектов: 100% (4/4), готово.
При сжатии изменений используется до 6 потоков
Сжатие объектов: 100% (3/3), готово.
Запись объектов: 100% (4/4), 372 bytes | 372.00 KiB/s, готово.
Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
remote: 
remote: Create a pull request for 'master' on GitHub by visiting:
remote:      https://github.com/Korsunmaster/LABS_MOBILS/pull/new/master
remote: 
To github.com:Korsunmaster/LABS_MOBILS.git
 * [new branch]      master -> master
Ветка «master» отслеживает внешнюю ветку «master» из «origin».
```
Теперь наш проект загружен.

## Лабораторная работа № 5

По примеру 12 создаем докерфайл и в ту же директорию кидаем файл *index.html*. После этого создаем новый образ командой `docker build . -t example_volume_dockerfile` и запускаем контейнер командой `docker run -d -p 8000:80 example_volume_dockerfile`

После этого проинспектировав созданный контейнер увидим данные о точке монтирования
```bash
"Mounts": [
            {
                "Type": "volume",
                "Name": "81d4f60e8c648aafd3d113fd42a58082b37a7335d05541a17aea840cacf6a333",
                "Source": "/var/lib/docker/volumes/81d4f60e8c648aafd3d113fd42a58082b37a7335d05541a17aea840cacf6a333/_data",
                "Destination": "/usr/share/nginx/html",
                "Driver": "local",
                "Mode": "",
                "RW": true,
                "Propagation": ""
            }
        ]
```

Теперь же воостановим и соберем наш проект из прошлой лабораторной работы и подключим к нему *volume*:

Перейдем в терминале в папку с нашим проектом и выполним `docker build . -t myphp` далее создадим новый том `docker volume create storage`

И после этого создадим контейнер присоединив его к нашему тому: `docker run -d -v storage:/var/www/html -p 80:80 myphp`

Убедимся, что присоединение выполнено успешно:
```bash
"Mounts": [
            {
                "Type": "volume",
                "Name": "storage",
                "Source": "/var/lib/docker/volumes/storage/_data",
                "Destination": "/var/www/html",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ]
```

Таким же образом можно присоединить и **СУБД postgreSQL**: 

Удалим все имющиеся тома и контейнеры и создадим новый том `docker volume create storage`

После этого откроем в контейнере СУБД и примонтируем к вновь созданному тому: `docker run -v storage:/var/lib/postgresql/data --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres` 
> Команда для создания была взята из dockerhub postgres, а способ монтирования из практического задания в лабораторной работе. Точка монтирования из образа *postgres*.
```bash
"Mounts": [
            {
                "Type": "volume",
                "Name": "storage",
                "Source": "/var/lib/docker/volumes/storage/_data",
                "Destination": "/var/lib/postgresql/data",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ]
```

Теперь повторим пример 6 для образа *nginx:alpine*. Удалим и создадим вновь volume storage. Далее установим другую точку монтирования для тома */usr/share/nginx/html/* и изменим порт на *80*:

`docker container run -d -v storage:/usr/share/nginx/html/ -p 80:80 --name nginxalp nginx:alpine`

Перейдя по локальному ip на 80 порт можем увидеть измененную стартовую страницу с приветствием от *nginx*.

Следущей командой `sudo cp index.html /var/lib/docker/volumes/storage/_data` мы скопируем наш файл в volume.

И с помощью команды `sudo ls -l /var/lib/docker/volumes/storage/_data` увидим, что файл добавлен:
```bash
-rw-r--r-- 1 root root 153 мая 17 19:40 index.html
```

Также можно проверить работоспособность с помощью команды *curl*, ведь мы перебросили файл *index.html* в наш том, поэтому введя `curl http://localhost:80` получим:
```bash
<!doctype html>
<html>
  <head>
    <title>This is the title of the webpage!</title>
  </head>
  <body>
    <p>My one Dockerfile!!</p>
  </body>
</html>
```
