# Vagrant

```bash
# Создать новый Vagrantfile в текущей директории
vagrant init

# Запустить ВМ по Vagrantfile  
vagrant up

vagrant global-status

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
