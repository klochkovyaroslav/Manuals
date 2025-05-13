# IIS+PHP+MySQL
## 1. Установка и настройка веб-сервера IIS на Windows Server 2019

![image](https://github.com/user-attachments/assets/02e20d28-cd73-4db0-b601-f5d9006dd569)

![image](https://github.com/user-attachments/assets/d02fbf86-6c08-47c1-8bda-d2ece7f5978f)


#### Добавить сайт
![image](https://github.com/user-attachments/assets/8f2bfcff-0b0f-4507-bf0a-85a80a2eb710)


## 2. Установка PHP
#### Скачать с офф сайта архив
![image](https://github.com/user-attachments/assets/77a17d0c-d8ee-49ab-83d4-deeeb2b9b8b1)

![image](https://github.com/user-attachments/assets/8dea0af5-e096-490d-a354-663e807d764f)

#### Установка PHP и настройка конфигурационного файла "PHP.INI".

- Распаковать в "C:\Program Files\php"
- Найти файл "_php.ini-production_" и переименовать в "_php.ini_"
- Найти в файле "_php.ini_" строку "_;open_basedir =_" (Директория где будут разрешены PHP скрипты) 
```
open_basedir = C:\inetpub\wwwroot
```
##### Будут ли скрипты обрабатываться при прямом запросе или только при запросе от web сервера
- Найти строку "cgi.force_redirect = 1" и заменить на "0"
  ```
  cgi.force_redirect = 0
  ```

#### Проверяем работает ли PHP
![image](https://github.com/user-attachments/assets/c71dc735-da65-49aa-9d17-33d926af077c)
##### Если видим такой ответ, то:

#### Скачиваем и устанавливаем [Visual Studio](https://learn.microsoft.com/ru-ru/cpp/windows/latest-supported-vc-redist?view=msvc-170)

![image](https://github.com/user-attachments/assets/0cb0fbae-459d-4b4c-b139-37ea7dffa605)


## 3. Настрока Web сервера IIS

#### Настроить сопоставление модуля php-cgi 
![image](https://github.com/user-attachments/assets/ecb5ad89-a8b5-4a3d-8c51-81216c354a0b)

![image](https://github.com/user-attachments/assets/0c6f1cb7-6862-42c8-bb5e-a33ac2932081)

![image](https://github.com/user-attachments/assets/4edb90dd-f226-4ecf-863e-7f92505231b3)


## 4. Установить PHP Manager

![image](https://github.com/user-attachments/assets/5a897e4d-a857-4c9c-91c9-4b0b4b362c92)
![image](https://github.com/user-attachments/assets/a6217da4-3c0a-4aed-90df-ff0227960969)


## 5. Установить MySQL

![image](https://github.com/user-attachments/assets/43be60b2-dd3f-47e7-86b5-6dc32b3233d9)
![image](https://github.com/user-attachments/assets/d8940685-8431-4c8e-88b7-cc4265accb9f)
![image](https://github.com/user-attachments/assets/c9c53f40-a497-49d3-b893-585b5b04b419)
![image](https://github.com/user-attachments/assets/0859d977-21c0-4043-8d6d-a5e7fbbb0e71)
![image](https://github.com/user-attachments/assets/38645c8c-40ef-4990-b17c-699089db9967)
![image](https://github.com/user-attachments/assets/8cf7117a-10d6-4568-b2db-220b2bb6a0d6)
![image](https://github.com/user-attachments/assets/6b5ae479-e2c8-463b-8713-abb131170493)
![image](https://github.com/user-attachments/assets/4c3de76b-f168-4eea-b5a8-8e80fa190845)
![image](https://github.com/user-attachments/assets/b5ecab3a-61e7-457a-9eae-b116bb185cc2)
![image](https://github.com/user-attachments/assets/65aa2c14-a4a5-4bfa-bdd1-2cc08fbaa61d)










