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

### Cоздание: закрытого ключа и сформироавать запрос на сертификат для выпуска сертификата в стороннем CA.

```bash
sudo openssl req -out /tmp/workspad.csr -new -newkey rsa:2048 -nodes -keyout /tmp/private.key
```
```
Country Name (2 letter code) [XX]: RU
State or Province Name (full name) []: Kaliningrad region
Locality Name (eg, city) [Default City]: Kaliningrad
Organization Name (eg, company) [Default Company Ltd]: Softline
Organizational Unit Name (eg, section) []: оставляем пустым
Common Name (eg, your name or your server's hostname) []: sm-01.domain.local
Email Address []: klochkovyaroslav@gmail.com
Please enter the following 'extra' attributes to be sent with your certificate request A challenge password []: можно оставить пустым
An optional company name []: SL
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
### Информация о файле CSR запроса:
```bash
openssl req -text -noout -verify -in /tmp/request.csr
```
### Проcмотреть checksum сетификата и закрытого ключа (!!! должны совпадать):
```bash
sudo openssl x509 -noout -modulus -in /tmp/public_cert.crt | openssl md5
sudo openssl rsa -noout -modulus -in /tmp/private.key | openssl md5
```

## Конвертация сертификатов

### Конвертируем сертификат из CER в CRT
sudo openssl x509 -inform DER -in /tmp/cert.cer -out /tmp/cert.crt  
### Конвертировать в формат PEM
sudo openssl x509 -in /tmp/cert.cer -outform PEM -out /tmp/cert.crt
