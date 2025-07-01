# Как узнать версию LINUX

## Debian

#### Версия ОС:
```bash
sudo lsb_release -a или -d
sudo cat /etc/*-release
sudo cat /etc/os-release
sudo hostnamectl
sudo cat /etc/issue
sudo cat /etc/debian_version
cat /proc/version | awk '{print $6,$7}'
```

## RedHat

```bash
cat /etc/centos-release
cat /etc/*-release
hostnamectl
cat /proc/version | awk '{print $9,$10,$11}
cat /proc/cmdline
cat /proc/sys/kernel/{ostype,osrelease,version}
```

#### Версия ядра и версию GCC::

```bash
uname -a
cat /proc/version
```

### Как узнать архитектуру компьютера:

```bash
arch
uname -m
```


