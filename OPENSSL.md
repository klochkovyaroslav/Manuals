# OpenSSL

## Раздельное создание: закрытого ключа, запроса на сертификат, и выпуск самозаверенного-сертификата.

### Создание закрытого ключа

```bash
sudo openssl genpkey -algorithm rsa -pkeyopt rsa_keygen_bits:2048 -out /tmp/private.key  
```

### Создание запроса на сертификат

```bash
sudo openssl req -new -key /tmp/private.key -out /tmp/request.csr  
```

### Выдать самоподписанный сертификат

```bash
sudo openssl x509 -req -days 365 -in /tmp/request.csr -signkey /tmp/private.key -out /tmp/public_cert.crt
```

## Создание: закрытого ключа и выпуск самозаверенного-сертификата одной командой.

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /tmp/private.key -out /tmp/public_cert.crt
```

### Cоздание: закрытого ключа, запроса на сертификат для выпуска сертификата в стороннем CA.

```bash
sudo openssl req -out /tmp/workspad.csr -new -newkey rsa:2048 -nodes -keyout /tmp/private.key
```

## Просмотр информации о сертификате, ключе, запросе:

### Информации о сертификате:
```bash
sudo openssl x509 -noout -text -in /tmp/public_cert.crt

sudo openssl dhparam -text -noout -inform PEM -check -in /etc/openvpn-gost/easy-gost/keys/dh2048.pem
```
### Информации о закрытом ключе:
```bash
sudo openssl rsa -noout -text -in /tmp/private.key
```
### Проверка закрытого ключа:
```bash
sudo openssl rsa -in /tmp/private.key -check
```
### Проcмотреть checksum сетификата и закрытого ключа (!!! должны совпадать):
```bash
sudo openssl x509 -noout -modulus -in /tmp/public_cert.crt | openssl md5
sudo openssl rsa -noout -modulus -in /tmp/private.key | openssl md5
```
