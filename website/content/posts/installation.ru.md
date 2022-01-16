---
title: "Установка сервера"
date: 2022-01-16T19:28:59+03:00
draft: false
---

Установка сервера Eversity достаточно проста.

Вам потребуется:

- Школа (или любое другое учебное завделение), подключенное к платформе [Schools.by](https://schools.by)
- Сервер, поддерживающий OpenJDK 16 (в этой инструкции все шаги были воспроизведены на VPS с установленной Ubuntu 20.04,
1 ГБ ОЗУ, 1 ядро)
- ~2 ГБ интернет-трафика
- ~40 минут времени

У вас всё это есть? Отлично, приступим.

## Установка

_**Примечание: все шаги в этой инструкции сделаны для повторения на системе Ubuntu, но для остальных ОС процесс примерно похож**_

### 1. Установка OpenJDK 16

Один из простейших шагов. Выполните эту команду в терминале:

``` bash
sudo apt install -y openjdk-16-jre
```
Теперь проверьте, что всё установилось.

``` bash
java -version
```
Вывод этой команды должен быть примерно таким:

```
openjdk version "16.0.2" 2021-07-20
OpenJDK Runtime Environment (build 16.0.2+7-Ubuntu-2)
OpenJDK 64-Bit Server VM (build 16.0.2+7-Ubuntu-2, mixed mode, sharing)
```

### 2. Установка и настройка PostgreSQL

Установите PostgreSQL:
``` bash
sudo apt install -y postgresql-13
```

После установки, запустите базу данных:
``` bash
sudo service postgresql start
```

Затем, откройте командную строку БД:
``` bash
sudo -u postgres psql
```

Теперь, выполните следующие команды:

``` sql
CREATE USER eversity WITH PASSWORD 'ВПИШИТЕ СВОЙ ПАРОЛЬ'; /* Создаёт пользователя eversity с заданным паролем */
CREATE DATABASE eversity; /* Создаёт базу данных 'eversity' */
GRANT ALL ON DATABASE eversity TO eversity; /* Выдаёт все права на базу данных пользователю 'eversity' */
```

Отлично! Теперь на вашем сервере установлена база данных!

### 3. Получите Eversity Server

Откройте [систему сборки Eversity Server](https://app.circleci.com/pipelines/github/enrollie/Eversity-Server), найдите последний запуск (самый верхний), откройте выполнение "build",
дальше откройте вкладку "ARTIFACTS" и скачать JAR-файл сервера. Сохраните его под названием "Eversity-Server.jar"

### 4. Настройте всё

#### 4.1. Настройте именование школы

[Скачайте](https://github.com/enrollie/Eversity-Server/raw/main/school_naming.properties.template) файл "school_naming.properties.template", переименуйте в "school_naming.properties" (уберите часть '.template') и измените под свои нужды

#### 4.2. Настройте сервер

[Скачайте](https://github.com/enrollie/Eversity-Server/raw/main/src/main/resources/application.conf) файл "application.conf" и отредактируйте под свою конфигурацию.

### 5. Запустите сервер

Создайте файл с названием "run.sh" и дайте ему право на запуск:
``` bash
touch run.sh
chmod +x ./run.sh
```

Далее, впишите туда следующее:
``` bash
#!/bin/bash
java -jar Eversity-Server.jar -config=application.conf
```

Запустите сервер командой
``` bash
./run.sh
```

Если программа выдала следующее (без учёта времени)
> Application started in 2.012 seconds

, то поздравляем! Сервер Eversity успешно настроен и готов к работе!
