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







```
