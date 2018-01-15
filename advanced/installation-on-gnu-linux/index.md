# Title: Installation unter GNU/Linux
<!-- Position: 5 -->
---
Alle folgenden Beispiele sind Out-of-the-Box Installationen mit dem Nginx Webserver.
Falls für andere Distributionen weitere Schritte erforderlich sind, teile uns diese bitte über das [Forum](https://forum.bludit.org) mit oder bearbeite einfach diese Seite auf Github (Einen Link findest du am Ende dieser Seite).

### Inhalt
1. [Installation unter Ubuntu 16.04](#ubuntu)
2. [Installation unter Centos 7 / RedHat 7](#centos)

---

## <a id="ubuntu"></a> Installation unter Ubuntu 16.04 LTS

Vorbedingungen:
- PHP-FPM läuft unter dem Usernamen `www-data`.
- PHP-FPM horcht auf den Unix socket auf `unix:/run/php/php7.0-fpm.sock`.
- Nginx läuft unter dem Usernamen `www-data`.
- Es ist kein anderer Webserver installiert.
- Dies ist eine Basisinstallation. Lies das 'Read more' für Produktivumgebungen.

Installation des Nginx Webserver, PHP und nützlichen Tools.
```
$ sudo apt install -y nginx php-fpm php-dom php-mbstring php-cli php-gd php-opcache unzip wget
```

Konfiguration Nginx.
```
$ sudo rm -f /etc/nginx/sites-enabled/*
```

Hinzufügen einer neuen Datei mit dem Virtual Server Block unter `/etc/nginx/conf.d/bludit.conf`
```
server {
	listen 80;
	server_name _;
	root /www/bludit;
	index index.php;

	location ~ \.php$ {
		fastcgi_pass    unix:/run/php/php7.0-fpm.sock;
		include         fastcgi.conf;
	}

	location / {
		try_files $uri $uri/ /index.php?$args;
	}
}
```

Herunterladen und Entpacken der neuesten Bludit-Version.
```
$ mkdir /www
$ cd /www
$ wget https://s3.amazonaws.com/bludit-s3/bludit-builds/bludit_latest.zip
$ unzip bludit_latest.zip
$ sudo chown -R www-data:www-data /www
```

Neustarten der Services damit die neue Konfiguration herangezogen wird.
```
$ sudo service php7.0-fpm restart
$ sudo service nginx restart
```

Im Browser nun die Seite http://localhost um die Installation abzuschliessen.

---

## <a id="centos"></a> Installation unter Centos 7 / Red Hat 7

Vorbedingungen:
- PHP-FPM läuft unter dem Usernamen `nginx`.
- PHP-FPM horcht auf den Unix socket auf `unix:/run/php/php-fpm.sock`.
- Nginx läuft unter dem Usernamen `nginx`.
- Es ist kein anderer Webserver installiert.
- Dies ist eine Basisinstallation. Lies das 'Read more' für Produktivumgebungen.

```
$ sudo yum install -y epel-release
```

Installation des Nginx Webserver, PHP und nützlichen Tools.
```
$ yum install -y nginx php-fpm php-cli php-dom php-mbstring php-zip php-gd
```

Konfiguration Nginx und Hinzufügen einer neuen Datei mit dem Virtual Server Block unter `/etc/nginx/conf.d/bludit.conf`
```
server {
	listen 80;
	server_name _;
	root /www/bludit;
	index index.php;

	location ~ \.php$ {
		fastcgi_pass    unix:/run/php/php7.0-fpm.sock;
		include         fastcgi.conf;
	}

	location / {
		try_files $uri $uri/ /index.php?$args;
	}
}
```

Herunterladen und Entpacken der neuesten Bludit-Version.
```
$ mkdir /www
$ cd /www
$ wget https://s3.amazonaws.com/bludit-s3/bludit-builds/bludit_latest.zip
$ unzip bludit_latest.zip
$ sudo chown -R nginx:nginx /www
```

Neustarten der Services damit die neue Konfiguration herangezogen wird.
```
$ sudo systemctl php-fpm restart
$ sudo systemctl nginx restart
```

Im Browser nun die Seite http://localhost um die Installation abzuschliessen.
