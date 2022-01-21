---
layout: default
title: Configuration
nav_order: 4
permalink: /configuration
---

---
<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}

</details>
---

## Set up Virtual Hosts

With name-based virtual hosting, you can host more than one website (also called a "virtual host" or "vhost") on each IP address.
There's a default virtual host named `Example`. If you don't need any more virtual hosts, you can skip this step. 

### Set up DNS
{: .no_toc}
Forward the domain names of your web sites to the IP address used by your web server. This is commonly done by adding an `A` name entry to the DNS zone file for the website. This is not part of OpenLiteSpeed configuration. Please see your DNS provider for further instruction, if needed.

### Create Virtual Host Directories
{: .no_toc}
1. Create directories for your new virtual host. In this example, we've named our virtual host `Example2`. From the command line, add an `Example2` directory, plus three subdirectories `conf`,`html`, and `logs`, in your LSWS directory, like so:
```bash
mkdir /usr/local/lsws/Example2
mkdir /usr/local/lsws/Example2/{conf,html,logs}
```
2. Change the owner of the `conf` directory to `lsadm:lsadm` (the WebAdmin Console user) so that only the WebAdmin Console will have the ability to manipulate Example2's configuration.
```bash
chown lsadm:lsadm /usr/local/lsws/Example2/conf
```
3. Add the `Example2` virtual host to the WebAdmin Console. Navigate to **Virtual Hosts > Add**, and configure the settings like so:
  - **Virtual Host Name** = `Example2`
  - **Virtual Host Root** = `$SERVER_ROOT/Example2`
  - **Config File** = `$SERVER_ROOT/conf/vhosts/Example2/vhost.conf`
  - **Enable Scripts/ExtApps** = `Yes` 
  - **Restrained** = `No`
4. You might see the following warning if you are starting from scratch with this virtual host's configuration. Go ahead and click to create:
```
file /usr/local/lsws/conf/vhosts/Example2/vhost.conf does not exist. CLICK TO CREATE
``` 
5. Click the **Save** button, return to Example2's configuration, and change the following settings under the **General** tab:
  - **Document Root** = `/usr/local/lsws/Example2/html`
  - **Index Files** = `index.html, index.php`
6. We recommend enabling the Rewrite feature as well. Change the following settings under the **Rewrite** tab:
  - **Enable Rewrite** = `Yes`
  - **Auto Load from .htaccess** = `yes`

## Set up Listeners

### Create a Listener 
{: .no_toc}
Navigate to **Listeners** in the WebAdmin Console. There is already a default listener that listens to all IPs on port 8088, but you might want to create another two listeners for both HTTP and HTTPS ports. Here is how:
1. Click the **Add** button to create an HTTP listener with following settings:
  - **Listener Name** = `HTTP`
  - **IP Address** = `ANY IPv4`
  - **Port** = `80`
  - **Secure** = `No`
2. Click the **Add** button to create an HTTPS listener with following settings:
  - **Listener Name** = `HTTPS`
  - **IP Address** = `ANY IPv4`
  - **Port** = `443`
  - **Secure** = `Yes`
3. To configure SSL on the listener you named `HTTPS`, navigate to **Listeners > HTTPS > SSL**, and click the **Edit** button. Specify the private key and certificate path, then click the **Save** button. Here are some examples, but be sure to use your own correct file path:
    - Let's Encrypt:
        - **Private Key File** = `/etc/letsencrypt/live/example.com/privkey.pem`
        - **Certificate File** = `/etc/letsencrypt/live/example.com/fullchain.pem`
        - **Chained Certificate** = `Yes`
    - Temporary WebAdmin certificate or a [private self-signed certificate](https://stackoverflow.com/questions/10175812/how-to-create-a-self-signed-certificate-with-openssl):
        - **Private Key File** = `/usr/local/lsws/conf/example.key`
        - **Certificate File** = `/usr/local/lsws/conf/example.crt`
        - **Chained Certificate** = `Not Set`

### Map Virtual Hosts
{: .no_toc}
Navigate to **Listeners > HTTP > Virtual Host Mappings** and click the **Add** button. Use the following values to map the HTTP listener to Example2 virtual host:
  - **Virtual Host** = `Example2`
  - **Domains** = `example2.com, www.example2.com`
Repeat for the HTTPS listener using the exact same settings.

## Set up PHP

### Method 1: WebAdmin Console

#### Define External Applications at the Server Level
{: .no_toc}

This example creates an external application for PHP 8.0, however, you can use the default external applications with some modification, if you prefer. 

1. Navigate to **Server Configuration > External App > Add**
2. Choose `LiteSpeed SAPI App` for the application type
3. Give the new external application an appropriate name, address, maximum number of connections, etc., using the following example as a guide:
  - **Name**: `lsphp80`
  - **Address**: `uds://tmp/lshttpd/lsphp80.sock`
  - **Max Connections**: `10`
  - **Environment**: 
  ```PHP_LSAPI_CHILDREN=10
LSAPI_AVOID_FORK=200M```
  - **Initial Request Timeout (secs)**: `60`
  - **Persistent Connection**: `Yes`
  - **Start By Server**: `1` 
  - **Command**: `lsphp80/bin/lsphp`
  - **Back Log**: `100`
  - **Instances**: `1`
  - **Memory Soft Limit (bytes)**: `2047M`
  - **Memory Hard Limit (bytes)**: `2047M`
  - **Process Soft Limit**: `1400`
  - **Process Hard Limit**: `1500`

#### Set up Script Handlers at the Server Level 
{: .no_toc}

1. Navigate to **Server Configuration > Script handler > Add**
2. Give the new script handler appropriate settings, using the following example as a guide:
  - **Suffixes**: `php`
  - **Handler Type**: `LiteSpeed SAPI`
  - **Handler Name**: `lsphp80`

#### Set up PHP at the Virtual Host Level 
{: .no_toc}
If you want to use different settings for any of your virtual hosts, you can set up the same external applications and script handlers at the virtual host level, which will override any server-level script handler settings. 
1. Navigate to **Server Configuration > Virtual Hosts > Example2 > Script Handlers > Add**, and update the new script handler with appropriate settings, using the following example as a guide:
  - **Suffixes**: `php`
  - **Handler Type**: `LiteSpeed SAPI`
  - **Handler Name**: `lsphp80`

### Method 2: Command Line Interface 
{: .d-inline-block }
Advanced
{: .label .label-yellow }

#### Define External Applications for PHPs
{: .no_toc}
Edit the `httpd_config.conf` file as follows:

```bash
extProcessor lsphp80{
    type                            lsapi
    address                         uds://tmp/lshttpd/lsphp.sock
    maxConns                        35
    env                             PHP_LSAPI_CHILDREN=35
    env                             LSAPI_AVOID_FORK=200M
    initTimeout                     60
    retryTimeout                    0
    persistConn                     1
    pcKeepAliveTimeout
    respBuffer                      0
    autoStart                       1
    path                            lsphp80/bin/lsphp
    backlog                         100
    instances                       1
    priority                        0
    memSoftLimit                    2047M
    memHardLimit                    2047M
    procSoftLimit                   1400
    procHardLimit                   1500
}
```

#### Set up Script Handlers 
{: .no_toc}
Add the following to the `httpd_config.conf` file:

```bash
scriptHandler{
    add lsapi:lsphp80  php
}
```

## Set up SSL for Virtual Hosts

OpenLiteSpeed supports Server Name Indication (SNI), allowing users to set SSL certificates at the virtual host level. To set up SSL for our Example2 virtual host:

1. Navigate to **Virtual Hosts > Example2 > SSL > Edit SSL Private Key & Certificate**
2. Update the new SSL settings with the following example as a guide, using your own file paths:
  - **Private Key File** = `/usr/local/lsws/conf/example.key`
  - **Certificate File** = `/usr/local/lsws/conf/example.crt`
  - **Chained Certificate** = `Not Set`

**Note 1**: There is no need to set an additional listener for different domains or SSL

**Note 2**: Even if you have SSL set up in the virtual host, you do not want to leave the port 443 listener's SSL empty

## Set up Caching
The OpenLiteSpeed Cache Module is installed and enabled by default, so you don't need to change any settings. 

This is a server-level cache, so you will still need to enable caching for your web apps. This can be done simply and easily with our [LSCache plugins](https://docs.litespeedtech.com/lscache/#available-plugins), or by using [rewrite rules](https://docs.litespeedtech.com/lscache/noplugin/) in `.htaccess` if no plugin is available.


