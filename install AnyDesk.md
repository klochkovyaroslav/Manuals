- add repository key to Trusted software providers list  

```bash
wget -qO - https://keys.anydesk.com/repos/DEB-GPG-KEY | apt-key add -  
```

- add the repository:  

```bash
echo "deb http://deb.anydesk.com/ all main" > /etc/apt/sources.list.d/anydesk-stable.list  
```

- update apt cache:  

```bash
apt update  
```

- install anydesk:  

```bash
apt install anydesk  
```
