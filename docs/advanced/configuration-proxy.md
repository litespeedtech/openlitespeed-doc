---
layout: default
title: OpenLiteSpeed as Reverse Proxy
parent: Advanced
permalink: /docs/advanced/proxy
nav_order: 5
---

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}

</details>

# Setting up OpenLiteSpeed as a reverse proxy

OpenLiteSpeed can be set up as a transparent reverse proxy to any backend web server or application server that supports HTTP. OpenLiteSpeed proxies to other servers by setting them as external applications.
Once you have set up your web server external application, you will have to set which traffic OpenLiteSpeed should send to that external application. This can be done in a number of ways: via rewrite rules, contexts, or script handlers.

## Create a Web Server External Application 

You do do it either Server level or Virtual Host level. 
To set up external application at server level, you add a Web Server in the **WebAdmin Console**: Navigate to **Server Configuration > External App > Add > Type**

The most important setting is Address. Set your backend server up to listen on this address and port. For simplicity, we've set our copy of Apache to listen on port 8080.

  - **Name**                   = `apache`
  - **Address**                 = `localhost:8080`
  - **Max Connections**         = `100`
  - **Initial Request Timeout** = `60`
  - **Retry Timeout**           = `0`
  - **Response Buffering**      = `No`

Now you have OpenLiteSpeed ready to proxy to your backend server. next, we need to setup [rewrite rules](#method-3-proxying-with-rewrite-rules), [contexts](#method-1-proxy-with-context), or [script handlers](#method-2-proxy-with-script-handlers) so 
OpenLiteSpeed knows what traffic to send to this external application. 

**Note:** If you are using IP-based virtual hosting, you will need to set up a different web server external application for each vhost, as each web server external application only reroutes traffic to a single IP address.


## Proxy Method

For this guide we will show the methos by using the default virtual host, `Example` as example, and proxy files to an Apache backend.

### Method 1: Proxy with Context
{: .d-inline-block }
Flexible to add more contexts ahead
{: .label .label-green }

Proxying with contexts allows you to add other context functionality on top of your proxying.
Using a proxy context to designate which traffic to proxy has the advantage of allowing you to easily set aside a location on your site to be proxied. 

#### Create Context
{: .no_toc}

Navigate to **Virtual Host Configuration > [Example] > Add > Type > Proxy**, and add a Proxy context
With the following settings, we will be proxying all the locations on this site to the web server internal app.

  - **URI**        =  `/`
  - **Web Server** =  `[Server Level]: apache`

### Method 2: Proxy with Script Handlers
{: .d-inline-block }
proxy certain kinds of content
{: .label .label-green }

Setting up a script handler mapped to your web server external application allows you to easily proxy certain types of requests (determined by the suffix used in the request). This can be a simple way to set up OpenLiteSpeed to handle most content, but send requests for certain special content (Java scripts or scripts that require Apache, to name two examples) to a different backend. 

#### Create a Script Handler
{: .no_toc}

Navigate to **Virtual Host Configuration > [Example] > Script Handler > Add** and add a Script Handler

  - **Suffixes**      =  `html`
  - **Handler Type**  =  `Web Server`
  - **Handler Name**  =  `apache`

### Method 3: Proxying with Rewrite Rules
{: .d-inline-block }

name-based virtual hosting
{: .label .label-green }

One of the most versatile ways to designate which traffic to send is through rewrite rules. We think rewrite rules are the most convenient way to proxy all traffic for a specific virtual host. Rewrite rules can do far more than just this, though. By editing rewrite rules as specified in Apache's [mod_rewrite documentation](http://httpd.apache.org/docs/current/mod/mod_rewrite.html), one can direct the server to proxy traffic based on suffix or URI or many other variables.

#### Create a Rewrite Rule
{: .no_toc}

Navigate to **Virtual Host Configuration > [Example] > Rewrite > > Rewrite Rules > Add** and add a Rewrite Rule

 - For IP-Based Virtual Hosting
  If you're using IP-based virtual hosting on your backend (each domain has its own IP), then you can use the REWRITE RULES as follows:
```bash
REWRITERULE ^(.*)$ HTTP://apache:8080/$1 [P]
```

- For Name-Based Virtual Hosting
  If you're using name-based virtual hosting (many domains using one IP), you need to add another variable to your rewrite rule to signal which backend virtual host you intend the traffic to go to: (This ability is unique to LiteSpeed and our web server as an external application setup.)
  Assume we have set up both port 80(apachehttp) and 443(apachehttps) External Applications.
```bash
RewriteCond %{HTTPS} !=on
REWRITERULE ^(.*)$ HTTP://apachehttp/$1 [P,L,E=PROXY-HOST:WWW.EXAMPLE1.COM]
RewriteRule ^(.*)$ HTTPS://apachehttps/$1 [P,L,E=PROXY-HOST:WWW.EXAMPLE1.COM]
```
**Note:** 
    - "apache/apachehttp/apachehttps" is the name of a proxy (web server) external application you have created.
    - For IP-based virtual hosting, you will need to set up a different web server external application for each vhost, as each web server external application only reroutes traffic to a single IP address.
    - Add "https://" in front if the external web server uses HTTPS. Port is optional if the external web server uses the standard ports.
    - For more advanced situations, please see [Advanced Reverse Proxy](https://openlitespeed.org/kb/advanced-reverse-proxy/).

## Map the Proxy vhost to Your Listener(s)

Navigate to **Listeners > [listener Name] > Virtual Host Mappings > Add**

  - **Virtual** =  `Host: proxy-vhost`
  - **Domain**  =  `proxy-vhost.domain.com`

