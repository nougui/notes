## Mailjet

### Postfix
```sh
sudo apt update && sudo apt -y install postfix libsasl2-modules
```

```sh
sudo nano /etc/postfix/main.cf
```

```
#default_transport = error
#relay_transport = error
```

```
relayhost = in-v3.mailjet.com:2525
smtp_tls_security_level = encrypt
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
```

```sh
sudo nano /etc/postfix/sasl_passwd
```

```
in-v3.mailjet.com:2525 api:key
```

```sh
sudo postmap /etc/postfix/sasl_passwd
```

```sh
sudo ls -l /etc/postfix/sasl_passwd*
```

```sh
sudo rm /etc/postfix/sasl_passwd
```

```sh
sudo chmod 600 /etc/postfix/sasl_passwd.db
```

```sh
sudo ls -la /etc/postfix/sasl_passwd.db
```

```sh
sudo /etc/init.d/postfix restart
```

```sh
sudo apt -y install mailutils
```

```sh
echo 'Test passed.' | mail -s 'Test-Email' -aFrom:cron@system.domain.com to@domain.com
```

```sh
sudo tail -n 5 /var/log/syslog
```
