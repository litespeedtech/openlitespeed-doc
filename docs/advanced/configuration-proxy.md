---
layout: default
title: Use OLS as Reverse Proxy
parent: Advanced
permalink: /docs/advanced/proxy
nav_order: 2
---


<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}

</details>

# Setting up OpenLiteSpeed as a Reverse Proxy

OpenLiteSpeed can be set up as a transparent reverse proxy to any backend web server or application server that supports HTTP. OpenLiteSpeed proxies to other servers by setting them as external applications.

Once you have set up your web server external application, you will have to set which traffic OpenLiteSpeed should send to that external application. This can be done in a number of ways: via rewrite rules, contexts, or script handlers.

## Create a Web Server External Application 

You can do this at either the server level or the virtual host level.
 
To set up a server-level external application, navigate to **Server Configuration > External App > Add > Type** in the WebAdmin Console.

When updating the settings, the most important thing is **Address**. Set your backend server up to listen on this address and port. For simplicity, we've set our copy of Apache to listen on port 8080.

  - **Name**                   = `apache`
  - **Address**                 = `localhost:8080`
  - **Max Connections**         = `100`
  - **Initial Request Timeout** = `60`
  - **Retry Timeout**           = `0`
  - **Response Buffering**      = `No`

Now you have OpenLiteSpeed ready to proxy to your backend server. The next step is to set up [rewrite rules](#method-1-proxying-with-rewrite-rules), [contexts](#method-2-proxy-with-context), or [script handlers](#method-3-proxy-with-script-handlers) so that OpenLiteSpeed knows which traffic to send to this external application. 

**Note:** If you are using IP-based virtual hosting, you will need to set up a different web server external application for each vhost, as each web server external application only reroutes traffic to a single IP address.

Once you have set up your preferred proxy method, don't forget to [map the proxy virtual host to your listeners](#map-the-proxy-vhost-to-your-listeners).

## Proxy Method

For this guide we will use the default `Example` virtual host and proxy files to an Apache backend.

### Method 1: Proxying with Rewrite Rules
{: .d-inline-block }
Best for name-based virtual hosting
{: .label .label-green }

One of the most versatile ways to designate which traffic to send is through rewrite rules. We think rewrite rules are the most convenient way to proxy all traffic for a specific virtual host. Rewrite rules can do far more than just this, though. By editing rewrite rules as specified in Apache's [mod_rewrite documentation](http://httpd.apache.org/docs/current/mod/mod_rewrite.html), one can direct the server to proxy traffic based on suffix or URI or many other variables.

#### Create a Rewrite Rule
{: .no_toc}

Navigate to **Virtual Host Configuration > Example > Rewrite > Rewrite Rules > Add** and add a Rewrite Rule.

##### IP-Based Virtual Hosting
If you're using IP-based virtual hosting on your backend (each domain has its own IP), then you can use rewrite rules as follows:
```bash
REWRITERULE ^(.*)$ HTTP://apache:8080/$1 [P]
```

##### Name-Based Virtual Hosting
If you're using name-based virtual hosting (many domains using one IP), you need to add another variable to your rewrite rule to signal which backend virtual host you intend the traffic to go to. This ability is unique to LiteSpeed and our web server as an external application setup. This example assumes you have set up both port 80(`apachehttp`) and 443(`apachehttps`) External Applications:
```bash
RewriteCond %{HTTPS} !=on
REWRITERULE ^(.*)$ HTTP://apachehttp/$1 [P,L,E=PROXY-HOST:WWW.EXAMPLE1.COM]
RewriteRule ^(.*)$ HTTPS://apachehttps/$1 [P,L,E=PROXY-HOST:WWW.EXAMPLE1.COM]
```

##### Notes
{: .no_toc}
- `apache`, `apachehttp`, and `apachehttps` are the names of proxy (web server) external applications you have presumably created. If your proxies have other names, use them instead.
- For IP-based virtual hosting, you will need to set up a different web server external application for each vhost, as each web server external application only reroutes traffic to a single IP address.
- Add `https://` in front if the external web server uses HTTPS. Port is optional if the external web server uses the standard ports.
- For more advanced situations, please see our [Advanced Reverse Proxy](https://openlitespeed.org/kb/advanced-reverse-proxy/) knowledgebase article.

### Method 2: Proxy with Context
{: .d-inline-block }
Flexibility for adding more later
{: .label .label-green }

Proxying with contexts allows you to add other context functionality on top of your proxying.
Using a proxy context to designate which traffic to proxy has the advantage of allowing you to easily set aside a location on your site to be proxied. 

#### Create Context
{: .no_toc}

Navigate to **Virtual Host Configuration > Example > Add > Type > Proxy**, and add a Proxy context.
With the following settings, we will be proxying all the locations on this site to the web server internal app.

  - **URI**        =  `/`
  - **Web Server** =  `[Server Level]: apache`

### Method 3: Proxy with Script Handlers
{: .d-inline-block }
Useful for distinguishing types of content
{: .label .label-green }

Setting up a script handler mapped to your web server external application allows you to easily proxy certain types of requests (determined by the suffix used in the request). This can be a simple way to set up OpenLiteSpeed to handle most content, but send requests for certain special content (Java scripts or scripts that require Apache, to name two examples) to a different backend. 

#### Create a Script Handler
{: .no_toc}

Navigate to **Virtual Host Configuration > Example > Script Handler > Add** and add a Script Handler

  - **Suffixes**      =  `html`
  - **Handler Type**  =  `Web Server`
  - **Handler Name**  =  `apache`

## Map the Proxy vhost to Your Listener(s)

Navigate to **Listeners > [listener Name] > Virtual Host Mappings > Add**

  - **Virtual** =  `Host: proxy-vhost`
  - **Domain**  =  `proxy-vhost.domain.com`

