---
layout: post
title:  Google Domains and Github Pages
date:   2017-05-16 17:22:00 -0700
comments: true
categories: web 
---

<a href="https://domains.google">![Google Domains](/assets/img/google_domains.png){: style="width:250px"}</a>
+
<a href="https://pages.github.com">![Github Pages](/assets/img/github_pages.png){: style="width:170px"}</a>
\\
**Build lazyself.io with Google Domains and Github Pages...**

<!--more-->

When I first thought about having a personal blog, I wanted to give it a custom domain name. So I registered domain name **lazyself.io** from [Google Domains][gd]. Google is a **Domain Name Registrar** who sells domain names and provides registration services. Google is meanwhile a **DNS Hosting Provider** who maintains DNS name servers for customers to use, which is neat that I can put my domain name on Google name servers to allow others to reach me from internet by my domain name.

Domain name is like an Address, I need a Website behind this address to let people visit. [Github Pages][gh] is a **Web Hosting Provider** who lets businesses and individuals make their websites accessible through the World Wide Web. So I put up this static blogging website on Github Pages. In order to link up my website with my domain name, I added the IP addresses of the Github Pages web servers to the Google Domains name servers. Now lazyself.io comes to life on the internet. Hooray!

----
<br>
<br>
**How to link up Github Pages web servers with Google Domains name servers?**
1. Go to the `Settings` of the Github repository where the website files are stored.
2. Type the custom domain name into `Custom domain` item, `Save`. You'll see `Your site is published at http://<custom domain name>`. Now Github Pages web servers have been done mapping the custom domain name to the default Github Pages domain name, something like <*username*>.github.io/<*blog*>. From now on, the Github Pages default domain will be redirected to the custom domain.\\
![Github Setting][gh-setting]
3. Go to `Google Domains`, click `My domains` and then click `the domain name`, click the `DNS` tab, scroll to the `Custom resource records` section.
4. Add an `A` type resource record with the web server IP addresses given by Github Pages <https://help.github.com/articles/setting-up-an-apex-domain/>. It could take a while(5 min for adding a new record, TTL for changing an existing record) for the DNS updates to take effect.\\
![Google Domain Setting][gd-setting]
5. To confirm that the DNS record is set up correctly and has been applied, use `dig` command to check with the custom domain name.
```bash
$ dig +noall +answer lazyself.io
lazyself.io.    3371  IN  A 192.30.252.154
lazyself.io.    3371  IN  A 192.30.252.153
```
6. Done, website and domain name are now linked up.

[gd]:         https://domains.google
[gh]:         https://pages.github.com
[gh-setting]: /assets/img/googleDomainsAndGithubPages/github_setting.png
[gd-setting]: /assets/img/googleDomainsAndGithubPages/google_domains_setting.png

