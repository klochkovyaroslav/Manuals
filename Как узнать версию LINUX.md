# Как узнать версию LINUX

## Debian
```bash
sudo lsb_release -a или -d
sudo cat /etc/*-release
sudo cat /etc/os-release
sudo hostnamectl
sudo cat /etc/issue
sudo cat /etc/debian_version
cat /proc/version | awk '{print $6,$7}
```

uname -a (версия ядра)

	Чтобы понять какой репозиторий используется в вашей системе  
sudo cat /etc/apt/sources.list 


	RPM:
cat /etc/centos-release
cat /etc/*-release
hostnamectl
cat /proc/version
cat /proc/version | awk '{print $9,$10,$11} - RedHat version

cat /proc/cmdline
cat /proc/sys/kernel/{ostype,osrelease,version}
rpm -qa | grep openssl

	Как узнать архитектуру компьютера:
arch
uname -m


