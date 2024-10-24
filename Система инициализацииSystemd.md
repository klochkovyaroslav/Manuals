# Управление загрузкой сервисов через систему инициализации systemd

#### Список юнитов, которые добавлены в systemd
```bash
systemctl list-units
```
#### Отобразить статус всех сервисов:
```bash
systemctl list-units --type service --all
```
#### Проверить включен ли сервис
```bash
systemctl is-enabled zabbix-web-service.service
```
#### Добавить в автозапуск
```bash
systemctl enable имя_сервиса
```
#### Удалить из автозапуска
```bash
systemctl disable имя_сервиса
```
