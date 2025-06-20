# VSS (Volume Shadow Copy Service) - Hyper-V

# LINUX
#### Установка и проверка компонент интеграции Hyper-V в Debian Linux
```bash
sudo apt install hyperv-daemons
```
#### Проверить что службы успешно запущены и страртуют автоматически
```bash
sudo systemctl status *hv-*
```

## fsfreeze в Linux: Заморозка файловой системы для консистентных снапшотов


#### Как посмотреть события fsfreeze в Linux
```bash
sudo joutnalctl -b | grep -i freeze
sudo journalctl --system | grep Hyper-V

sudo journalctl -b | grep fsfreeze
sudo dmesg | grep -i freeze
```

![image](https://github.com/user-attachments/assets/1735d323-b073-4516-8ca2-1d43c8af9a03)

