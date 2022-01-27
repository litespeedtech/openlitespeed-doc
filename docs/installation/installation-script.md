---
layout: default
title: Install from Script
parent: Installation
nav_order: 3
permalink: /installation/script
---

# Install from One-Click Script

Our One-Click script comes with several options. Here are two commmon usages.

- Install OpenLiteSpeed, LSPHP, MariaDB, WordPress, and LiteSpeed Cache plugin:
```bash
bash <( curl -k https://raw.githubusercontent.com/litespeedtech/ols1clk/master/ols1clk.sh ) -w
```
- Install OpenLiteSpeed and LSPHP only: 
```bash
bash <( curl -k https://raw.githubusercontent.com/litespeedtech/ols1clk/master/ols1clk.sh )
```

See below for additional options and usage examples.

## Options

Usage: `./ols1clk.sh [option] [option] …`


| Opt  | Options                        | Description                                                                                 |
| :--: | ------------------------------ | ------------------------------------------------------------------------------------------- |
| `-A` | `--adminpassword [PASSWORD]`   | To set the WebAdmin password for OpenLiteSpeed instead of using a random one.               |
| `-E` | `--email [EMAIL]`              | To set the administrator email.                                                             |
|      | `--lsphp [VERSION]`            | To set the LSPHP version, such as 80. We currently support versions '56 70 71 72 73 74 80'. |
|      | `--mariadbver [VERSION]`       | To set MariaDB version, such as 10.5. We currently support versions '10.2 10.3 10.4 10.5'.  |
| `-W` | `--wordpress`                  | To install WordPress. You will still need to complete the WordPress setup by browser        |
|      | `--wordpressplus [SITEDOMAIN]` | To install, setup, and configure WordPress, also LSCache will be enabled                    |
|      | `--wordpresspath [WP_PATH]`    | To specify a location for the new WordPress installation or use for an existing WordPress.  |
| `-R` | `--dbrootpassword [PASSWORD]`  | To set the database root password instead of using a random one.                            |
|      | `--dbname [DATABASENAME]`      | To set the database name to be used by WordPress.                                           |
|      | `--dbuser [DBUSERNAME]`        | To set the WordPress username in the database.                                              |
|      | `--dbpassword [PASSWORD]`      | To set the WordPress table password in MySQL instead of using a random one.                 |
|      | `--listenport [PORT]`          | To set the HTTP server listener port, default is 80.                                        |
|      | `--ssllistenport [PORT]`       | To set the HTTPS server listener port, default is 443.                                      |
|      | `--wpuser [WP_USER]`           | To set the WordPress admin user for WordPress dashboard login. Default value is wpuser.     |
|      | `--wppassword [PASSWORD]`      | To set the WordPress admin user password for WordPress dashboard login.                     |
|      | `--wplang [WP_LANGUAGE]`       | To set the WordPress language. Default value is "en\_US" for English.                       |
|      | `--sitetitle [WP_TITLE]`       | To set the WordPress site title. Default value is mySite.                                   |
| `-U` | `--uninstall`                  | To uninstall OpenLiteSpeed and remove installation directory.                               |
| `-P` | `--purgeall`                   | To uninstall OpenLiteSpeed, remove installation directory, and purge all data in MySQL.     |
| `-Q` | `--quiet`                      | To use quiet mode, won't prompt to input anything.                                          |
| `-V` | `--version`                    | To display the script version information.                                                  |
| `-v` | `--verbose`                    | To display more messages during the installation.                                           |
|      | `--update`                     | To update ols1clk from github.                                                              |
| `-H` | `--help`                       | To display help messages.                                                                   |

## Examples

| Examples                             | Description                                                                         |
| ------------------------------------ | ----------------------------------------------------------------------------------- |
| `./ols1clk.sh`                       | To install OpenLiteSpeed with a random WebAdmin password.                           |
| `./ols1clk.sh --lsphp 80`            | To install OpenLiteSpeed with lsphp80.                                              |
| `./ols1clk.sh -A 123456 -e a@cc.com` | To install OpenLiteSpeed with WebAdmin password "123456" and email <a@cc.com>.      |
| `./ols1clk.sh -R 123456 -W`          | To install OpenLiteSpeed with WordPress and MySQL root password "123456".           |
| `./ols1clk.sh --wordpressplus a.com` | To install OpenLiteSpeed with a fully configured WordPress installation at "a.com". |