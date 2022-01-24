---
layout: default
title: Configure Security
parent: Advanced
permalink: /docs/advanced/security
nav_order: 4
---

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}

</details>

## reCAPTCHA with OpenLiteSpeed

OpenLiteSpeed features reCAPTCHA as a method of defense against DDoS attack. 

When a visitor accesses the website, they will need to go though reCAPTCHA validation. After passing the reCAPTCHA validation, the visitor is temporarily allowlisted as long as they continue to browse the site. Once the visitor has become inactive, reCAPTCHA is once again enabled for that visitor's next request.

If the user fails to pass reCAPTCHA validation a few times, the server will return a 403 error and then drop the connection from that IP. 

### Enable reCAPTCHA at the Server Level

In the OpenLiteSpeed WebAdmin Console, navigate to **Configuration > Server > Security > LS reCAPTCHA**. Configure the following settings:

  - **Enable reCAPTCHA**: The master switch. This should be set to `Yes`. If you wish to use reCAPTCHA at the virtual host level, then you must first enable it here, at the server level. reCAPTCHA will be activated when the number of concurrent requests reaches the configured **Connection Limit**.
  - **reCAPTCHA Type**: The reCAPTCHA type to use with the key pairs. If a key pair has not been provided and this setting is set to `Not Set`, a default key pair of type `Invisible` will be used. `Checkbox` will display a check box reCAPTCHA for the visitor to validate.  `Invisible` will attempt to validate the reCAPTCHA automatically, and if successful, will redirect to the desired page. `Invisible` is the default, but for easy testing, you can switch to `Checkbox`. If you have trouble with the `Invisible` reCAPTCHA continuing to auto-refresh and then fail, change the type to `Checkbox`.
  - **Max Tries**: The maximum number of reCAPTCHA attempts permitted before denying the visitor. Default value is `3`.
  - **Connection Limit**: The number of concurrent connections (SSL & non-SSL) needed to activate reCAPTCHA. reCAPTCHA will be used until concurrent connections drop below this number. Initially you should set this number low enough for easy testing. For example, `2`.  The default value is `15000`, which makes it almost impossible to activate the reCAPTCHA.
  - **SSL Connection Limit**:  The number of concurrent SSL connections needed to activate reCAPTCHA. reCAPTCHA will be used until concurrent connections drop below this number.  Initially you should set this number low enough for easy testing. For example, `2`.  The default value is `10000`, which makes it almost impossible to activate the reCAPTCHA.

### Enable reCAPTCHA at the Virtual Host Level

Server-level reCAPTCHA must be enabled first, as it is the master switch. Then you can change virtual-host-level connection limits, which will override server level limits.

To change reCAPTCHA settings for the `Example` virtual host, navigate to **Configuration > Virtual Hosts > Example > Security > LS reCAPTCHA** and set **Enable reCAPTCHA** to `Yes`. Update the other settings as desired.

## Per-Client Throttling

OpenLiteSpeed includes a built-in Per-Client Throttling feature which allows you to block bad IPs.
Navigate to **Configuration > Server > Security configurations > Per Client Throttling** to find several configuration settings that you can use to limit the request, bandwidth, and connection rate per remote IP address.

### Request Throttling

Separate controls are available for throttling requests for static files and dynamic content.

### Bandwidth Throttling

The server allows setting separate bandwidth limits for inbound and outbound traffic. Bandwidth numbers will be rounded up in 4KB increments. Set to `0` to disable throttling. The **Outbound Bandwidth** limit allows serving more unique clients and prevents limited network bandwidth from getting used up by a small number of clients with fast network connections.

### Connection Throttling
These settings control concurrent connections coming from one client (IP address) and guard against DoS attacks.

- **Connection Hard Limit** controls how many concurrent connections are allowed from one IP address. If an IP reaches the hard connection limit, the web server will immediately close newly accepted connections from that IP address, and move on to pending connections from different IP addresses. As almost all web browsers support keep-alive/persistent connections (multiple requests pipelined through one connection), the number of connections required in normal browsing is very small. Typically, one connection is enough, but some web browsers try to establish additional connections to speed up downloading. Allowing 4 to 10 connections from one IP is recommended. Less than that will probably affect normal web services.
- Use **Connection Soft Limit**, **Grace Period**, and **Banned Period** to spot and mitigate abusers: An IP address that stays over the soft limit for the length of the grace period will be banned for the length of time set in **Banned Period**. This is a good way to identify IPs that should be added to the **Denied List**.

**Note**: The number of connections can temporarily exceed the soft limit during the grace period, as long as it is under the hard limit. After the grace period, if it is still above the soft limit, then no more connections will be allowed from that IP for duration of the banned period.

### Example
{: .no_toc}

Default values:
  - **Static Requests/second** = `0`
  - **Dynamic Requests/second** = `0`
  - **Outbound Bandwidth (bytes/sec)** = `0`
  - **Inbound Bandwidth (bytes/sec)** = `0`
  - **Connection Soft Limit** = `10000`
  - **Connection Hard Limit** = `10000`
  - **Block Bad Request** = `Not Set`
  - **Grace Period (sec)** = `15`
  - **Banned Period (sec)** = `300`

Updated values:
  - **Static Requests/second** = `40`
  - **Dynamic Requests/second** = `2`
  - **Outbound Bandwidth (bytes/sec)** = `0`
  - **Inbound Bandwidth (bytes/sec)** = `0`
  - **Connection Soft Limit** = `15`
  - **Connection Hard Limit** = `20`
  - **Block Bad Request** = `Yes`
  - **Grace Period (sec)** = `15`
  - **Banned Period (sec)** = `60`

Explanation: 
An IP that has established more than 20 connections with the web server, or has established over 15 connections of over 15 seconds (the grace period), is treated as a DoS-attacker. The server will ban the IP for 60 seconds and record a log entry in the error log file. 

**Note:** The hard limit can be adjusted based on an attacker's strategy. If the botnet is not very aggressive, you will need to lower the limit to just below their max connection per IP, to make sure it won't affect a regular user. If they only make very few connections per IP, do not use the hard limit to detect them.

### Add Trusted IPs to Allowlist
To exclude any IP from the client throttle limits (and bypass DDoS detection), add the IP with a trailing `T` (aka trusted) in **WebAdmin Console > Server > Security > Access Control > Allowed List**. 


