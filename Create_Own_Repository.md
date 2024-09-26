# FTP Repository

```bash
apt update
apt install vsftpd 
systemctl status vsftpd.service 
nano /etc/vsftpd.conf 
systemctl reload vsftpd.service 
systemctl restart vsftpd.service
mkdir -p /srv/ftp/my_as_repo/conf
touch /srv/ftp/my_as_repo/conf/distributions
nano /srv/ftp/my_as_repo/conf/distributions
содержание:

Origin: Debian
Codename: 1.7_x86-64
Suite: 1.7.3
Architectures: amd64
Components: main contrib non-free
SignWith: yes

gpg --gen-key (имя ключа as_173)
apt install reprepro

reprepro --ask-passphrase -b /srv/ftp/my_as_repo export
reprepro -b /srv/ftp/my_as_repo/ includedeb 1.7_x86-64 /tmp/zoom_amd64.deb

gpg --armor --output repo_pgp.key --export as_173

apt-key add repo_pgp.key

apt edit-sources
содержание:
deb ftp://localhost/my_as_repo 1.7_x86-64 main contrib non-free

apt update 
apt install zoom  
```
---

### Добавить диск CD/DVD в список репозитариев

```bash
sudo apt-cdrom add
``` 
