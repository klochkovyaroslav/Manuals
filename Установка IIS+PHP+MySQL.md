# Установка IIS+PHP+MySQL
## Установка и настройка веб-сервера IIS на Windows Server 2019

![image](https://github.com/user-attachments/assets/d1c0193f-840b-4757-9633-6eafee7a85c3)

![image](https://github.com/user-attachments/assets/612b9ba7-fe22-417a-b56a-36b45009bb8b)



## Установка PHP
#### Скачать с офф сайта архив
![image](https://github.com/user-attachments/assets/77a17d0c-d8ee-49ab-83d4-deeeb2b9b8b1)

![image](https://github.com/user-attachments/assets/8dea0af5-e096-490d-a354-663e807d764f)

#### Распаковать в "C:\Program Files\php"

#### Найти файл "php.ini"-production и переитменовать в "php.ini"
#### айти в файле "php.ini" строку "open_basedir =" дописать:
```
open_basedir = C:\inetpub\wwwroot
```
