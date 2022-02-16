---
layout: default
title: Install from Repository
parent: Installation
nav_order: 1
permalink: /installation/repo
---

# Install OpenLiteSpeed and PHP from LiteSpeed Repository
{: .no_toc} 

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}

</details>

## Install the LiteSpeed Repository

- Debian/Ubuntu
```bash
sudo wget -O - http://rpms.litespeedtech.com/debian/enable_lst_debian_repo.sh | sudo bash
```
- AlmaLinux 8 & CentOS 8
```bash
sudo rpm -Uvh http://rpms.litespeedtech.com/centos/litespeed-repo-1.3-1.el8.noarch.rpm
sudo yum install epel-release
```
- CentOS 7
```bash
sudo rpm -Uvh http://rpms.litespeedtech.com/centos/litespeed-repo-1.3-1.el7.noarch.rpm
sudo yum install epel-release
```

## Install OpenLiteSpeed

- Debian/Ubuntu
```bash
sudo apt-get install openlitespeed
```
- CentOS
```bash
sudo yum install openlitespeed
```

## Install LSPHP

This command will install `lsphp80` and `lsphp80-mysql` into `/usr/local/lsws/lsphp80/bin/lsphp`:

- Debian/Ubuntu
```bash
sudo apt-get install lsphp80 lsphp80-common lsphp80-mysql
```
- CentOS
```bash
sudo yum install lsphp80 lsphp80-common lsphp80-mysqlnd
```

To get a list of the LSPHP packages and extensions available, you can run the following:

- Debian/Ubuntu
```bash
sudo apt-cache search lsphp
```
- CentOS
```bash
sudo yum search lsphp
```

## Access the WebAdmin Console

Run the following command to set the WebAdmin password if needed:
```bash
sudo /usr/local/lsws/admin/misc/admpass.sh
```
To access the WebAdmin console, visit port `7080` of your domain (for example,`https://example.com:7080/`) and log in using the password you just set. 

## Next Step
{: .no_toc} 

- [Configuration](/configuration)
