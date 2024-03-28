# GIT_Tutorial

## Установка

```
sudo apt install git
sudo yum install git
```
## Первоначальная настройка

```
git config --global user.name "user_github"
git config --global user.email "user_github@gmail.com"
```
####  Конфирурационный файл git config лежит:

```
Linux:
git config -l
cat .gitconfig

Windows:
C:\Users\"user_windows"\.gitconfig
type C:\Users\"user_windows"\.gitconfig
```

## Начало наботы с Git:

#### Создать папку с названием проекта в Git и перейти в нее

```
git init .
```

#### Проверка

```
git status
```

## Команды

```
git status

git add *
или
git add .

git commit -m "Информация о коммите"
```

#### История коммитов

```
Вывести все комиты
git log

Вывести последний комит
git log -1

Вывести последний комит + информацию что коммитили
git log -1 -p
```

#### Вернуть файл на последний коммит

```
git restore file1.txt
```

#### Посмотреть какие изменения будут сделаны во время коммита

```
git diff --staged
```

## Git Clone
### Добавить с Github.org репозиторий на свой ПК

```
git clone https://github.com/user_github/Any_project.git
```


## Git Push
#### Загрузить в репозиторий на Github.org файлы/папки со своего КП

```
git push origin
Ввести логин + пароль
```

## Аунтификация в Github.org по SSH ключу
#### Сгенерировать SSH ключ локально на своем ПК

```
ls -la ~/.ssh
ssh-keygen
Пароль при создании ключей можно не указывать(просто жмем Enter)
```

#### Копируем содержимое публичного ключа:
```
cat ~/.ssh/id_rsa.pub
```
Идем в Github.org в профиль -> Settings -> SSH and GPG Keys  
Кнопка "New SSH key"  
Задаем имя ключа  
В поле "Key" вставить содержимое публичного ключа "id_rsa.pub"  

## На ПК Изменить ссылку удаленного репозртория с "HTTPS" на "SSH"

#### Посмотреть адрес текущего удаленного репозртория

```
git remote -v
```

#### Изменить адрес ссылки текущего удаленного репозртория

```
git remote set-url origin git@github.com:user_github/Any_project.git
                          
```

Теперь можно добавлять, удалять, изменять содержиржимое репозитория без логина+пароль.  

## Git Push
#### Загрузить в репозиторий на Github.org файлы/папки со своего КП

```
git push origin
```

