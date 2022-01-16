---
title: "Server installation"
date: 2022-01-16T16:36:40+03:00
draft: false
---

Installing Eversity is actually very easy!

For it you will need to have these things:

- School (or any other educational institution, really) that is connected to [Schools.by](https://schools.by) platform
- A working server that supports OpenJDK 16 (In this tutorial private VPS with Ubuntu 20.04. 1 GB RAM, 1 CPU core was used)
- IP address, available from the internet with public domain _(optional)_
- ~2 GB of Internet traffic
- ~20 minutes of time

Got it? Then let's start!
## Installation

_**Note: all commands from this tutorial are made for Ubuntu, but for any other platform whole proccess is roughly same**_
### 1. Get OpenJDK 16
It is one of most simplest steps. Execute next commands in your terminal:
```
sudo apt install -y openjdk-16-jre
```
Now, check that everything installed properly: 
```
java -version
```
It should output something like
``` text
openjdk version "16.0.2" 2021-07-20
OpenJDK Runtime Environment (build 16.0.2+7-Ubuntu-2)
OpenJDK 64-Bit Server VM (build 16.0.2+7-Ubuntu-2, mixed mode, sharing)
```

### 2. Install and configure PostgreSQL
Installation: 
```
sudo apt install -y postgresql-13
```
After everything installed, run this command to start database:
```
sudo service postgresql start
```
Then, drop into database shell:
```
sudo -u postgres psql
```
There, run next commands:
``` sql
CREATE USER eversity WITH PASSWORD 'put your password here'; /* Creates user with username 'eversity' and your password */
CREATE DATABASE eversity; /* Creates database named 'eversity' */
GRANT ALL ON DATABASE eversity TO eversity; /* Grants all permissions on database to user 'eversity' */
```
Cool! Now your server has eversity database! At this point, no additional settings are required.

### 3. Get Eversity Server
Actually, there are two ways to get Eversity Server:
#### 3.1 Build it by yourself
There is nothing hard. First, obtain Eversity Server source code:
``` bash
sudo apt install -y git # ensures that git is intalled
git clone https://github.com/enrollie/Eversity-Server.git
cd Eversity-Server/
```
From there, build server:
``` bash
chmod +x ./gradlew # ensures that you can run gradlew
./gradlew shadowJar
```
After build process ended, server JAR will be contained in build directory:
```
build/libs/eversity-server-*.jar
```
Copy it from there to previous directory and leave source code directory:
``` bash
mv build/libs/eversity-server-*.jar ../Eversity-Server.jar
cd ..
```

#### 3.2 Download prebuilt JAR

Go to Eversity Server [CircleCI](https://app.circleci.com/pipelines/github/enrollie/Eversity-Server), find last build job (top one), click on "build" Job, 
then click on "ARTIFACTS" tab and download Eversity Server's JAR.

### 4. Configure everything
#### 4.1 Get School Naming config file
Download "school_naming.properties.template" from this [link](https://github.com/enrollie/Eversity-Server/raw/main/school_naming.properties.template). Rename it to "school_naming.properties" (remove '.template' part).

Open it and edit to fit your school (or other educational institution).

_Please note, that this file needs to be contained in the same directory as Eversity Server JAR_

#### 4.2 Get server config file
Get [application.conf](https://github.com/enrollie/Eversity-Server/raw/main/src/main/resources/application.conf) from enrollie's GitHub. Open it and make it fit to your needs.

#### 4.3 Create launch file
Create file named "run.sh" (call it anything you like) and paste next contents into it:
``` bash
#!/bin/bash
java -jar Eversity-Server.jar -config=application.conf
```

Then, type next command into your terminal:
``` bash
chmod +x ./run.sh # Makes run.sh executable
./run.sh
```
Now, if you done everything correctly, you should see something like
> Application started in 2.091 seconds

If you see that, then congratulations! You've started your Eversity Server!
