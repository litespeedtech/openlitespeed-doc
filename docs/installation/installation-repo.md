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

```bash
sudo wget -O - https://repo.litespeed.sh | sudo bash
```

## Install OpenLiteSpeed

- Debian/Ubuntu
```bash
sudo apt-get -y install openlitespeed
```
- CentOS
```bash
sudo yum install epel-release -y
sudo yum install openlitespeed -y
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

The randomly generated WebAdmin password is stored in the `/usr/local/lsws/adminpasswd` file. 

Run the following command to set the WebAdmin password if needed:
```bash
sudo /usr/local/lsws/admin/misc/admpass.sh
```
To access the WebAdmin console, visit port `7080` of your domain (for example,`https://example.com:7080/`) and log in using the password you just set. 

## Next Step
{: .no_toc} 

- [Configuration](/configuration)
