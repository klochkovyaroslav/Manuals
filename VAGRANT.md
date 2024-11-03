# Vagrant

```bash
# Создать новый Vagrantfile в текущей директории
vagrant init

# Получать адреса не со стандартной сети 192.168.56.0/24 а с требуемой
#Создать файл 
sudo vi /etc/vbox/networks.conf
Вставить: * 192.168.1.0/24

# Запустить ВМ по Vagrantfile  
vagrant up

vagrant global-status

# To prune the invalid entries
vagrant global-status --prune

# Для обновления параметров ВМ
vagrant provision

vagrant ssh-config

# Подключитиься к ВМ по ssh
vagrant ssh

# Сохранить состояние ВМ и остановить ее
vagrant suspend

# Возобновить работу ВМ
vagrant resume

# Перезагрузить ВМ
vagrant reload

vagrant halt

vagrant destroy -f

vagrant destroy [id]

# Установить плагин для Vagrant
vagrant plugin install

# Список установленных плагинов
vagrant plugin list
```
