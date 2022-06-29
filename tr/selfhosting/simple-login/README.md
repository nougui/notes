## Simple Login

### DNS

```sh
sudo apt update && sudo apt install -y dnsutils
```

```sh
mkdir sl
mkdir sl/pgp
mkdir sl/db
mkdir sl/upload
```

```sh
openssl genrsa -out dkim.key 1024
openssl rsa -in dkim.key -pubout -out dkim.pub.key
```

```sh
dig @1.1.1.1 domain.com mx
```

```
10 tempmail.domain.com
```

```sh
dig @1.1.1.1 tempmail.domain.com a
```

```
domain.com
```

```sh
sed "s/-----BEGIN PUBLIC KEY-----/v=DKIM1; k=rsa; p=/g" dkim.pub.key | sed 's/-----END PUBLIC KEY-----//g' |tr -d '\n' | awk 1
```

```sh
dig @1.1.1.1 dkim._domainkey.domain.com txt
```

```sh
dig @1.1.1.1 domain.com txt
```

```sh
dig @1.1.1.1 _dmarc.domain.com txt
```

```sh
sudo docker network create -d bridge \
    --subnet=240.0.0.0/24 \
    --gateway=240.0.0.1 \
    sl-network
```

```sh
docker run -d \
    --name sl-db \
    -e POSTGRES_PASSWORD=POSTGRES_PASSWORD \
    -e POSTGRES_USER=dbadmin \
    -e POSTGRES_DB=simplelogin \
    -p 5432:5432 \
	-d --label=com.centurylinklabs.watchtower.enable=false \
    -v /path/to/simplelogin/sl/db:/var/lib/postgresql/data \
    --restart always \
    --network="sl-network" \
    postgres:12.1
```

```sh
sudo apt-get install -y postfix postfix-pgsql -y
```

```sh
nano /etc/postfix/main.cf
```

```
# POSTFIX config file, adapted for SimpleLogin
smtpd_banner = $myhostname ESMTP $mail_name (Ubuntu)
biff = no

# appending .domain is the MUA's job.
append_dot_mydomain = no

# Uncomment the next line to generate "delayed mail" warnings
#delay_warning_time = 4h

readme_directory = no

# See http://www.postfix.org/COMPATIBILITY_README.html -- default to 2 on
# fresh installs.
compatibility_level = 2

# TLS parameters
smtpd_tls_cert_file=/path/to/ssl/domain.com/fullchain.pem
smtpd_tls_key_file=/path/to/ssl/domain.com/privkey.pem
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
smtp_tls_security_level = may
smtpd_tls_security_level = may

# See /usr/share/doc/postfix/TLS_README.gz in the postfix-doc package for
# information on enabling SSL in the smtp client.

alias_maps = hash:/etc/aliases
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 240.0.0.0/24

# Set your domain here
mydestination =
myhostname = tempmail.domain.com
mydomain = domain.com
myorigin = domain.com

relay_domains = pgsql:/etc/postfix/pgsql-relay-domains.cf
transport_maps = pgsql:/etc/postfix/pgsql-transport-maps.cf

# HELO restrictions
smtpd_delay_reject = yes
smtpd_helo_required = yes
smtpd_helo_restrictions =
    permit_mynetworks,
    reject_non_fqdn_helo_hostname,
    reject_invalid_helo_hostname,
    permit

# Sender restrictions:
smtpd_sender_restrictions =
    permit_mynetworks,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain,
    permit

# Recipient restrictions:
smtpd_recipient_restrictions =
   reject_unauth_pipelining,
   reject_non_fqdn_recipient,
   reject_unknown_recipient_domain,
   permit_mynetworks,
   reject_unauth_destination,
   reject_rbl_client zen.spamhaus.org,
   reject_rbl_client bl.spamcop.net,
   permit
   
```

```sh
nano /etc/postfix/pgsql-relay-domains.cf
```

```
# postgres config
hosts = localhost
user = dbadmin
password = POSTGRES_PASSWORD
dbname = simplelogin

query = SELECT domain FROM custom_domain WHERE domain='%s' AND verified=true
    UNION SELECT '%s' WHERE '%s' = 'domain.com' LIMIT 1;
```

```sh
nano /etc/postfix/pgsql-transport-maps.cf
```

```
# postgres config
hosts = localhost
user = dbadmin
password = POSTGRES_PASSWORD
dbname = simplelogin

# forward to smtp:127.0.0.1:20381 for custom domain AND email domain
query = SELECT 'smtp:127.0.0.1:20381' FROM custom_domain WHERE domain = '%s' AND verified=true
    UNION SELECT 'smtp:127.0.0.1:20381' WHERE '%s' = 'domain.com' LIMIT 1;
```

```sh
sudo systemctl restart postfix
```

```sh
nano /path/to/simplelogin/simplelogin.env
```

```
# WebApp URL
URL=http://tempmail.domain.com

# domain used to create alias
EMAIL_DOMAIN=domain.com

# transactional email is sent from this email address
SUPPORT_EMAIL=support@domain.com

# custom domain needs to point to these MX servers
EMAIL_SERVERS_WITH_PRIORITY=[(10, "tempmail.domain.com.")]

# By default, new aliases must end with ".{random_word}". This is to avoid a person taking all "nice" aliases.
# this option doesn't make sense in self-hosted. Set this variable to disable this option.
DISABLE_ALIAS_SUFFIX=1

# the DKIM private key used to compute DKIM-Signature
DKIM_PRIVATE_KEY_PATH=/dkim.key

# DB Connection
DB_URI=postgresql://dbadmin:POSTGRES_PASSWORD@sl-db:5432/simplelogin

FLASK_SECRET=FLASK_SECRET

GNUPGHOME=/sl/pgp

LOCAL_FILE_UPLOAD=1
```

```sh
docker run --rm \
    --name sl-migration \
    -v /path/to/simplelogin/sl:/sl \
    -v /path/to/simplelogin/sl/upload:/code/static/upload \
    -v /path/to/simplelogin/dkim.key:/dkim.key \
    -v /path/to/simplelogin/dkim.pub.key:/dkim.pub.key \
    -v /path/to/simplelogin/simplelogin.env:/code/.env \
	-d --label=com.centurylinklabs.watchtower.enable=false \
    --network="sl-network" \
    simplelogin/app:3.4.0 flask db upgrade
```

```sh
docker run --rm \
    --name sl-init \
    -v /path/to/simplelogin/sl:/sl \
    -v /path/to/simplelogin/simplelogin.env:/code/.env \
    -v /path/to/simplelogin/dkim.key:/dkim.key \
    -v /path/to/simplelogin/dkim.pub.key:/dkim.pub.key \
	-d --label=com.centurylinklabs.watchtower.enable=false \
    --network="sl-network" \
    simplelogin/app:3.4.0 python init_app.py
```

```sh
docker run -d \
    --name sl-app \
    -v /path/to/simplelogin/sl:/sl \
    -v /path/to/simplelogin/sl/upload:/code/static/upload \
    -v /path/to/simplelogin/simplelogin.env:/code/.env \
    -v /path/to/simplelogin/dkim.key:/dkim.key \
    -v /path/to/simplelogin/dkim.pub.key:/dkim.pub.key \
    -p 7777:7777 \
	-d --label=com.centurylinklabs.watchtower.enable=false \
    --restart always \
    --network="sl-network" \
    simplelogin/app:3.4.0
```

```sh
docker run -d \
    --name sl-email \
    -v /path/to/simplelogin/sl:/sl \
    -v /path/to/simplelogin/sl/upload:/code/static/upload \
    -v /path/to/simplelogin/simplelogin.env:/code/.env \
    -v /path/to/simplelogin/dkim.key:/dkim.key \
    -v /path/to/simplelogin/dkim.pub.key:/dkim.pub.key \
    -p 20381:20381 \
	-d --label=com.centurylinklabs.watchtower.enable=false \
    --restart always \
    --network="sl-network" \
    simplelogin/app:3.4.0 python email_handler.py
```

```sh
docker run -d \
    --name sl-job-runner \
    -v /path/to/simplelogin/sl:/sl \
    -v /path/to/simplelogin/sl/upload:/code/static/upload \
    -v /path/to/simplelogin/simplelogin.env:/code/.env \
    -v /path/to/simplelogin/dkim.key:/dkim.key \
    -v /path/to/simplelogin/dkim.pub.key:/dkim.pub.key \
	-d --label=com.centurylinklabs.watchtower.enable=false \
    --restart always \
    --network="sl-network" \
    simplelogin/app:3.4.0 python job_runner.py
```
