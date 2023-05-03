# Установить Docker и docker-compose Debian/Ubuntu

https://docs.docker.com/engine/install/debian/


#### Удалить старую версию:  
*`sudo apt-get remove docker docker-engine docker.io containerd runc`*  

#### Обновить информацию обо всех пакетах:
*`sudo apt-get update`*  

#### Установка дополнительных компонентов:  
*`sudo apt-get install ca-certificates curl gnupg lsb-release`*  

#### Добавить Ключ для Debian:  
*`sudo mkdir -m 0755 -p /etc/apt/keyrings`*  

*`curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg`*

#### Добавить Ключ для Ubuntu:  
*`sudo mkdir -m 0755 -p /etc/apt/keyrings`*  

*`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg`*

#### Команда для установки репозитория Debian:  
*`echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`*  

#### Команда для установки репозитория Ubuntu:  
*`echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`*

#### Обновить информацию обо всех пакетах:

*`sudo apt-get update`*  

#### Если ошибка с ключем:  
*`sudo chmod a+r /etc/apt/keyrings/docker.gpg`*
*`sudo apt-get update

### Установка Docker Engine, containerd, и Docker Compose:  

*`sudo apt-get install docker-ce docker-ce-cli containerd.io`*  
или
*`sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`*  


#### Установка Docker Compose:  

*`sudo curl -L "https://github.com/docker/compose/releases/download/v2.16.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`*  

*`sudo chmod +x /usr/local/bin/docker-compose`* 

#### Проверка версии: 

*`docker --version`*  

*`docker-compose --version`* 

*`docker compose version | awk '{print $4}'`*



# Установить Docker и docker-compose на Centos 7


#### Удалить старую версию:  
*`sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine`*

### Осуществить установку и настройку актуальных пакетов, о наличии которых нужно узнать на сайте производителя Docker:  
#### Установка репозитория:  

*`yum install -y yum-utils`*  
*`yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`*  
*`yum makecache fast`*  

*`yum -y install docker-ce`*  
или  

#### Установка Docker Engine, containerd, и Docker Compose:  
*`yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`*  

#### Настроить запуск Docker со стартом системы:  
*`systemctl enable docker`*

#### Запустить Docker с помощью команды:  
*`systemctl start docker`*

#### Выполнить установку компонентов docker-compose  
*`curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose`*  

#### Настроить права доступа к установленным компонентам:  
*`chmod +x /usr/local/bin/docker-compose`*  
*`ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose`*  

#### Осуществить проверку версии установленных компонентов:  

*`docker --version`*  

*`docker-compose --version`* 

*`docker compose version | awk '{print $4}'`*

#### Для ограничения параметров логирования выполнить команду создания файла daemon.json с редактированием, посредством редактора vi:  
*`vi /etc/docker/daemon.json`*  
#### Установить максимальные значения в файле daemon.json:  

{  
"log-driver": "json-file",  
"log-opts": {  
"max-size": "100m",  
"max-file": "10"  
}  
}  


## Docker и docker-compose Установлены и настроены на Centos 7


### Установить docker-образы серверных компонентов из архива  

Загрузить контейнеры:  
*`docker load -i emm-docker.tar.gz`*  
#### Посмотреть загруженние контейнеры:  
*`docker images -a`*  
#### Запустить все контейнеры:  
*`docker-compose up -d`*  
#### Посмотреть запущенные контейнеры:  
*`docker ps -a`*  
#### Остановить все контейнеры:  
*`docker-compose down -v`*  




## К основным командам docker можно отнести:


*`docker info`*  

*`docker network ls`*  

*`docker network inspect "имя контенера"`*  

#### Показать все контейнеры  
*`docker ps -a`*  

#### Команда для запуска в контейнере  
*`docker exec -t -i nginx bash`*  
-i: в интерактивном режиме  
-t: терминал  
-d, --detach: Запуститиь контейнер в бекграунде

#### Получать новые логи по мере появления  

*`docker logs -f --tail 10 nginx`*  


*`docker search tomcat`* – поиск образа tomcat в dockerHUB  
*`docker pull`* – скачивает образ с dockerHUB    
*`docker run`* – запускает контейнер на основе образа  
*`docker ps`* – вызывает список запущенных контейнеров  
*`docker exec`* – позволяет выполнять команды в контейнере  
*`docker stop`* – останавливает контейнер  
*`docker rm`* – удаляет контейнер  
*`docker rmi`* – удаляет образ  


*`docker pull mariadb`*  
*`docker images`*  
*`docker run -p 127.0.0.1:3306:3306  --name mariadb -e MARIADB_ROOT_PASSWORD=superpass -d mariadb`*  
*`docker ps`*  
*`docker run --name adminer --link mariadb:db -p 8080:8080 -d adminer http://127.0.0.1:8080/`*  
*`docker exec -ti adminer sh`*  
*`cat /etc/hosts`*  
*`docker stop mariadb adminer`*  
*`docker rm mariadb adminer`*  
*`docker rmi mariadb adminer`*  
*`docker build -t my_new_image:1.0.0`*  
-t: задаем имя образа  
: задает тег для образа  

*`docker run -d -p 8080:80 my_new_image:1.0.0`*  
*`docker tag my_new_image:1.0.0 my_new_image_ch:1.2.1`* -сделать копию образа с новым тегом.

*`docker exec -ti "ID контейнера или имя контейнера"`*  

#### Команда для создания нового образа из существующего контейнера
Если в работающий контейнер внесены были изменения, можно создать на его основе новый образ.  

*`docker ps`*  
*`docker commit "ID работающего контейнера" my_new_image:2.0.1`*  
*`docker images`*  


#### Export/Import Docker образа в файл архива.  

*`docker save my_new_image:2.0.1 > arch_name.tar`*  
*`docker load -i arch_name.tar`*  
