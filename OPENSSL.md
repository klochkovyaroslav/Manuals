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
