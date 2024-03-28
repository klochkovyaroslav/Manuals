# GIT_Tutorial

## Установка

```
sudo apt install git
sudo yum install git
```
## Первоначальная настройка

```
git config --global user.name "Klochkov Yaroslav"
git config --global user.email "klochkovyaroslav@gmail.com"
```
####  Конфирурационный файл git config лежит:

```
Linux:
git config -l
cat .gitconfig

Windows:
C:\Users\yaroslav.klochkov\.gitconfig
type C:\Users\yaroslav.klochkov\.gitconfig
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
