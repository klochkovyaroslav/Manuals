# Установка IIS+PHP+MySQL
## Установка и настройка веб-сервера IIS на Windows Server 2019

![image](https://github.com/user-attachments/assets/d1c0193f-840b-4757-9633-6eafee7a85c3)

![image](https://github.com/user-attachments/assets/612b9ba7-fe22-417a-b56a-36b45009bb8b)



## Установка PHP
#### Скачать с офф сайта архив
![image](https://github.com/user-attachments/assets/77a17d0c-d8ee-49ab-83d4-deeeb2b9b8b1)

![image](https://github.com/user-attachments/assets/8dea0af5-e096-490d-a354-663e807d764f)

#### Настройка конфигурационного файла.

- Распаковать в "C:\Program Files\php"
- Найти файл "php.ini"-production и переименовать в "php.ini"
- Найти в файле "php.ini" строку ";open_basedir =" и дописать:
##### Директория где будут разрешены PHP скрипты
```
open_basedir = C:\inetpub\wwwroot
```
##### Будут ли скрипты обрабатываться при прямом запросе или только при запросе от web сервера
- Найти строку "cgi.force_redirect = 1" и заменить на "0"
  ```
  cgi.force_redirect = 0
  ```

#### Проверяем работает ли PHP
![image](https://github.com/user-attachments/assets/d4f6e656-69c1-436d-bf04-f36bbfcf9f6d)

#### Скачиваем и устанавливаем [Visual Studio](https://learn.microsoft.com/ru-ru/cpp/windows/latest-supported-vc-redist?view=msvc-170)

![image](https://github.com/user-attachments/assets/0cb0fbae-459d-4b4c-b139-37ea7dffa605)

