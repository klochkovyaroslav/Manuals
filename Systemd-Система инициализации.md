# Управление загрузкой сервисов через систему инициализации systemd

#### Список юнитов, которые добавлены в systemd:
```bash
systemctl list-units
```
#### Отобразить статус всех сервисов:
```bash
systemctl list-units --type service --all
```
#### Проверить включен ли сервис:
```bash
systemctl is-enabled zabbix-web-service.service
```
#### Добавить в автозапуск:
```bash
systemctl enable nginx.service
```
#### Удалить из автозапуска:
```bash
systemctl disable nginx.service
```


#### Замаскировать сервис:
```bash
systemctl mask nginx.service
```
#### Снять маску:
```bash
systemctl unmask nginx.service
```
