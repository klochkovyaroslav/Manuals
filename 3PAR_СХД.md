# HP 3PAR

СОДЕРЖАНИЕ:  

[1. Снять логи для ТП](#Снять-логи-для-ТП)  
[2. Добавить сертификат в сервис консоль](#Добавить-сертификат-в-сервис-консоль)


## 3PAR 8400
#### Снять логи для ТП
##### Запустить 3PAR SERVICE CONSOLE

![image](https://github.com/user-attachments/assets/6af1d7c2-8eba-421a-810e-b7280b73fcc9)

##### Нажать ACTION

![image](https://github.com/user-attachments/assets/d17719fd-2735-49a3-8cca-659f5b970d99)

![image](https://github.com/user-attachments/assets/624410b2-3791-4d80-89cd-ec8d3fb4557d)

##### Выбрать "InSplore data" и выбрать нужное

![image](https://github.com/user-attachments/assets/1bd7a5fc-7ec0-4235-80ac-047339fdbc0f)

##### После завершения процесса сбора нажать в меню "Files"
##### Нажать ACTION
![image](https://github.com/user-attachments/assets/d260ab8d-e2b7-4a05-9c85-b023dc386905)

![image](https://github.com/user-attachments/assets/d3f37990-bb81-4208-96df-bbd145fff0b8)

##### Посмотреть процесс сбора "InSplore data"

![image](https://github.com/user-attachments/assets/4418efb8-2745-47ac-9567-42614b504bae)

-----

# HP StoreOnce 5100
##### Собрать логи
Старый интерфейс  
![image](https://github.com/user-attachments/assets/b422cb52-122e-4b8a-9ba6-ebdae6cf01fb)

Новый интерфейс  
![image](https://github.com/user-attachments/assets/bfbd94d1-14ed-420d-983b-14bfa0445458)


---

## Добавить сертификат в сервис консоль

Вводим команду: и (копируем название HP_3PAR 8400-CZ37442X9H)
```bash
showcert
```
Displays the certificates in human
```bash
showcert -text
```
Пересоздаем самоподписанный сертификат, вводим команду:
```bash
createcert unified-server -selfsigned -CN "HP_3PAR 8400-CZ37442X9H" -keysize 2048  -days 730
```
Удаляем и переподключаем массив в SSMC Administrator Console  
Принимаем сертификат в SSMC Administrator Console  
