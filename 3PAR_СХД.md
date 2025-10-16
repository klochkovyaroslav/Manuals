# HP 3PAR

СОДЕРЖАНИЕ:  

[1. Снять логи для ТП](#Снять-логи-для-ТП)  
[2. Перевыпустить сертификат](#Перевыпустить-сертификат-HP-3PAR)


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

---

## Перевыпустить сертификат HP 3PAR

#### Вводим команду: и (копируем название HP_3PAR 8400-CG33442X9B)
```bash
showcert
```
<img width="985" height="70" alt="image" src="https://github.com/user-attachments/assets/24f188f5-0429-42b2-92c6-c10415a0d5e0" />

#### Посмотреть информацию о сертификате
```bash
showcert -text
```
<img width="618" height="924" alt="image" src="https://github.com/user-attachments/assets/bacf8fcc-8a43-4159-b81f-1b51007eae59" />


#### Пересоздаем самоподписанный сертификат, вводим команду:
```bash
createcert unified-server -selfsigned -CN "HP_3PAR 8400-CG33442X9B" -keysize 2048 -days 730
```
#### Удаляем и переподключаем массив в SSMC Administrator Console  

<img width="600" height="524" alt="image" src="https://github.com/user-attachments/assets/3aa00046-c2e3-4975-92b8-d85a03d9c7cf" />
<img width="1911" height="447" alt="image" src="https://github.com/user-attachments/assets/37dbe0bc-9292-40ad-bf36-22f31d5dbe5d" />


#### Принимаем сертификат в SSMC Administrator Console  
<img width="531" height="464" alt="image" src="https://github.com/user-attachments/assets/9a373201-e833-462f-a9a3-79d2e8e4bc27" />
<img width="587" height="358" alt="image" src="https://github.com/user-attachments/assets/a0b37947-4b4b-49e9-80c8-ac17edab184e" />
<img width="1889" height="756" alt="image" src="https://github.com/user-attachments/assets/7bc70c64-cb5d-4aa8-a067-e491e72beaa1" />


---


# HP StoreOnce 5100
##### Собрать логи
Старый интерфейс  
![image](https://github.com/user-attachments/assets/b422cb52-122e-4b8a-9ba6-ebdae6cf01fb)

Новый интерфейс  
![image](https://github.com/user-attachments/assets/bfbd94d1-14ed-420d-983b-14bfa0445458)
