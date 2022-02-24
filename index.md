---
layout: default
title: Home
nav_order: 1
description: "OpenLiteSpeed is a high-performance, lightweight, open source HTTP server."
permalink: /
---

# High Performance Web Server
{: .fs-9 }

OpenLiteSpeed (OLS) is a high-performance, lightweight, open-source web server, which provides multiple easy setup methods to choose from.
{: .fs-6 .fw-300 }

[Get started now](#getting-started){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 } [View OLS on GitHub](https://github.com/litespeedtech/openlitespeed){: .btn .fs-5 .mb-4 .mb-md-0 }

---

OpenLiteSpeed is the Open Source edition of LiteSpeed Web Server Enterprise. Both servers are actively developed and maintained by the same team, and are held to the same high-quality coding standard. OLS contains all of the essential features found in LiteSpeed Enterprise, and represents our commitment to support the Open Source community.

## Getting Started
- [Installation](/installation/repo) 
- [Configuration](/configuration)

## Alternative Setup Methods
{: .d-inline-block }
Fast
{: .label .label-green }

- [Launch from Image](/installation/image) 
- [Install from Script](/installation/script) 
- [Launch from Docker](/installation/docker) 


## Supported Operating Systems 

OpenLiteSpeed supports current and non-EOL versions of the following Linux distributions:

- [RedHat Enterprise Linux](https://developers.redhat.com/products/rhel/download) and derivatives
  - [AlamaLinux](https://mirrors.almalinux.org/isos.html)
  - [CentOS](https://www.centos.org/download/)
  - [CloudLinux](https://docs.cloudlinux.com/cloudlinux_installation/#installing-new-servers)
  - [Oracle Linux](https://yum.oracle.com/oracle-linux-isos.html)
  - [RockyLinux](https://rockylinux.org/download/)
  - [VzLinux](https://vzlinux.org/)
- [Debian](https://www.debian.org/distrib/) and [Ubuntu](https://www.ubuntu.com/download)

## About OpenLiteSpeed

OpenLiteSpeed is &copy; 2013-{{ "now" | date: "%Y" }} by [LiteSpeed Technologies](https://www.litespeedtech.com/), and is distributed under a [GPLv3 license](https://www.litespeedtech.com/open-source/openlitespeed).

Learn more at [openlitespeed.org](https://openlitespeed.org).


## Contributors
Many thanks to our OpenLiteSpeed Documentation contributors!

<ul class="list-style-none">
{% for contributor in site.github.contributors %}
  <li class="d-inline-block mr-1">
     <a href="{{ contributor.html_url }}"><img src="{{ contributor.avatar_url }}" width="32" height="32" alt="{{ contributor.login }}"/></a>
  </li>
{% endfor %}
</ul>


