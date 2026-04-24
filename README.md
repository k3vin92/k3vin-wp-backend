# Wichtig:

Bis her hat das definieren von SSL und URL via compose nicht funktioniert. Diese mussten manuell nach dem Setup in der wp-config.php zuoberst gesetzt werden. Ich weiss noch nicht warum:

## 1. mit wp container als root verbinden um nano zu isntalieren und .php-Datei zu bearbeiten:

## 2. in die wp-config.php zuoberst nach <?php einfügen:

?php

if (
isset($\_SERVER['HTTP_X_FORWARDED_PROTO']) &&
$\_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https'
) {
$\_SERVER['HTTPS'] = 'on';
}

define('WP_HOME', 'https://wp.XXXXXXXXXX.ch');
define('WP_SITEURL', 'https://wp.XXXXXXXXXX.ch');
define('FORCE_SSL_ADMIN', true);

# Dump laden von Duplicator:

docker cp ./duplicator-dump/site-backup.zip wp_ernst_wordpress:/var/www/html/
docker cp ./duplicator-dump/installer.php wp_ernst_wordpress:/var/www/html/

http://localhost/duplicator-temp/installer.php

HOST: COMPOSE_SERVICE_NAME_VON_DB
DATABASE: WORDPRESS_DB_NAME (ENV)
USER: MARIADB_USER (ENV)
Pasword: MARIADB_PASSWORD (ENV)

-> Prod: danach installer.php & zip löschen!

Für Schreibrechte:

# Besitzer auf www-data setzen (UID 33, GID 33)

sudo chown -R 33:33 ./wp-content/uploads

# Schreibrechte setzen

sudo chmod -R 775 ./wp-content/uploads

# Zusatz Config WP-CONFIG.PHP

WORDPRESS_CONFIG_EXTRA … defaults to nothing, the value will be evaluated by the eval() function in wp-config.php. This variable is especially useful for applying extra configuration values this image does not provide by default such as WP_ALLOW_MULTISITE; see docker-library/wordpress#142 for more details.

```yaml
environment:
  WORDPRESS_CONFIG_EXTRA: |
    define('FORCE_SSL_ADMIN', true);
    if (!empty($$_SERVER['HTTP_X_FORWARDED_PROTO']) && $$_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
        $$_SERVER['HTTPS']='on';
    }
```

# Theme updaten;

- Neues Theme per sftp uploaden (seperat gemouted)
- in den ordner je nach dem:

```bash
  sudo rsync -av --chown=33:33 brauereiernstv5/ wp-content/themes/brauereiernstv5/
```

# Deploy:

# DB Conncetin:

docker exec -it wp_k3_db mariadb -u wp_db_k3_user -p

Links untersuchen:

SHOW DATABASES;

USE XYZ_db;

SELECT option_name, option_value
FROM WPPREFIX_options
WHERE option_name IN ('home','siteurl');
