# Magento Docker

# Usage

Clone or Download
```
$ git clone https://github.com/tieugum/magento-docker.git
```

Update or copy file env for docker
```
cd magento-docker
cp ./docker /PATH_MAGENTO_PROJECT/dev/docker
cp ./docker/env.docker.php /PATH_MAGENTO_PROJECT/app/etc/env.php
cp ./docker-compose.yml /PATH_MAGENTO_PROJECT/docker-compose.yml
```

Up and run:
```
docker-compose up -d
```

Prepare your magento installation:

```
# Enter `php` container
docker-compose exec php bash

cd /var/www/html/magento

cp auth.json.sample auth.json
chmod 777 auth.json

# Edit <public-key> <private-key> in file auth.json

mkdir /root/.composer
cp auth.json /root/.composer/auth.json

composer install

# Run this to install db if it is new installation,
php bin/magento setup:install \
    --base-url=http://localhost:8080/ \
    --db-host=mysql \
    --db-name=magento \
    --db-user=magento \
    --db-password=secret \
    --backend-frontname=admin \
    --admin-firstname=Super \
    --admin-lastname=Admin \
    --admin-email=admin@example.com \
    --admin-user=admin \
    --admin-password=admin123 \
    --language=en_US \
    --currency=USD \
    --timezone=America/Chicago \
    --use-rewrites=1 \
    --search-engine=elasticsearch7 \
    --elasticsearch-host=es \
    --elasticsearch-port=9200

# Update config

# Config to use varnish
php bin/magento config:set system/full_page_cache/caching_application 2
php bin/magento config:set system/full_page_cache/varnish/backend_host nginx
php bin/magento config:set system/full_page_cache/varnish/backend_port 80

# Flush cache
php bin/magento cache:flush

# Disable module magento twofactorauth
php bin/magento module:disable Magento_TwoFactorAuth
php bin/magento setup:upgrade
php bin/magento setup:di:compile
php bin/magento setup:static-content:Deploy -f
chmod -R 777 var/ generated/ pub/
```
