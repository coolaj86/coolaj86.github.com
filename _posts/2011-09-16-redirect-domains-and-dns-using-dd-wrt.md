---
layout: article
uuid: F35E2B91-17CD-4A4F-88BB-ED1C459E4322
title: Redirect Domains and DNS using DD-WRT
name: redirect-domains-and-dns-using-dd-wrt
created_at: 2011-09-16
updated_at: 2011-09-16
categories: unfinished
---

Scenario
===

You want to ensure that a particular (or any or all) URL redirects to a certain device on your network (like many wifi hotspots redirect to themselves).

Furthermore, if the user has configured their computer to use OpenDNS (208.67.222.222, 208.67.220.220), Google DNS (8.8.8.8, 8.8.4.4), or [another free dns provider](http://theos.in/windows-xp/free-fast-public-dns-server-list/), perhaps [the fastest one](http://code.google.com/p/namebench/), you'd still like to redirect them.

Recap:

  * using dd-wrt such as [WRT54G-TM](http://www.ebay.com/sch/i.html?_nkw=wrt54g-tm&_sacat=58058&_odkw=wrt54g-tm), [RT-N16](http://www.newegg.com/Product/Product.aspx?Item=N82E16833320038), or any linux device with some network cards
  * override normal DNS results
  * redirect custom DNS queries

Example Applications:

  * You want to pull an April Fool's prank on your friend
  * You want to steal credit card info from neighbors that shop through Amazon on your router
  * You're working with a very specific combination of systems and know that the end-user can't remember IP addresses well.

Important Note
===

All of these settings are given with the assumption that you have a fresh install of DD-WRT with the default options.

If that's not the case, hopefully you're familiar enough with DD-WRT to know that you need to turn on DNSMasq, etc, if you've turned them off.

Redirecting Hosts
===

Instead of the cliche `example.com` or `domain.tld`, here I'm using `foobar3000.com`, which is a **real test site**.

For this example we need 

  * known-good IP addresses that are likely to stick around
  * some test site(s)
  * DNSMasq redirections

**The [big boys](http://mostpopularwebsites.net/)**

    Google - 74.125.224.81
    Yahoo! - 67.195.160.76 (ugly)
    Bing - 65.55.175.254
    Amazon - 72.21.211.176
    Facebook - 69.63.189.11
    Wikipedia - 208.80.152.2 (ugly)
    Craigslist - 208.82.238.129

**Test Sites**

    Foobar3000 - 109 169  56 223
    HelloWorld3000 - 109 169  56 223
    example.com - N/A
    example.org - N/A

**Redirection**

Go to `Services -> Services -> DNSMasq`: <http://192.168.1.1/Services.asp>

    address=/echo.foobar3000.com/69.63.189.11
    address=/.foobar3000.com/72.21.211.176
    address=/foobar3000.com/65.55.175.254
    address=/.com/67.195.160.76
    address=/#/74.125.224.81

Once saved and applied, this behavior can be expected:

    109 169  56 223  <-  nslookup hello.echo.foobar3000.com
     69  63 189  11  <-        nslookup echo.foobar3000.com
     72  21 211 176  <-       nslookup hello.foobar3000.com
     65  55 175 254  <-             nslookup foobar3000.com
     67 195 160  76  <-                nslookup example.com
     74 125 224  81  <-                nslookup example.org

Redirecting DNS
===

So that works pretty well if the user is using the router's DNS,
however, there are many circumstances where that isn't the case -
either for performance or to circumvent a security policy.

**Note**: **test first** with `Run Commands`, then `Save Startup` if it actually works as you expect.

Go to `Administration -> Commands -> Command Shell`: <http://192.168.1.1/Diagnostics.asp>

Or login to the router

    telnet 192.168.1.1 # username:root password:admin

To disable DNS completely:

    iptables -I FORWARD 1 -p tcp --dport 53 -j DROP
    iptables -I FORWARD 2 -p udp --dport 53 -j DROP
    iptables -L # shows table

To redirect DNS to the router:

    iptables -t nat -A PREROUTING -i br0 -p udp --dport 53 -j DNAT --to $(nvram get lan_ipaddr)
    iptables -t nat -A PREROUTING -i br0 -p tcp --dport 53 -j DNAT --to $(nvram get lan_ipaddr)
    iptables -t nat -L -v -n # shows nat table

Note: I didn't always have success with `Run Commands`, but going in through `telnet` worked quite well.

Set router DNS
===

In case the router isn't going to be set up with DHCP you may want to provide backup DNS.

Go to `Setup -> Basic Setup -> Network Setup -> Network Address Server Settings (DHCP)`:

    Static DNS 1 0.0.0.0
    Static DNS 2 8.8.8.8
    Static DNS 3 8.8.4.4

Note: 0.0.0.0 means to use the default address obtained through DHCP (from the larger network / ISP to the router). Google's DNS (8.8.8.8, 8.8.4.4) provides a reliable alternative for non-DHCP network setups.

Go to `Services -> Services -> DHCP Server -> Additional DHCPd Options`:

    strict-order

This means that the DNS entries are tried in order (0.0.0.0 aka the default from the ISP will be first)

Once you save and apply this is the behaviour you can expect:

    Laptop Mode   Router Mode   End-user Result
    DHCP          DHCP          Gets ISP DNS (normal, as expected)
    DHCP          static        Gets Google DNS since the ISP failed to provide one
    static        DHCP          User's DNS is hijacked and replaced with the ISP DNS
    static        static        User's DNS is hijacked and replaced with Google DNS

In every case, the domains that you have overridden are served with the IP address you specified.

TODO
===

It would be nice to be able to restore the custom DNS settings of a particular user once the desired redirects have happened.

I don't know how that would be possible without sizeable effort.

Resources
===

In no particular order of easiness or importance:

  * <http://www.instructables.com/id/URL-Redirect-Prank-using-dd-wrt/?ALLSTEPS>
  * <http://forums.opendns.com/comments.php?DiscussionID=10977>
  * <http://bassmadrigal.com/blog/2008/08/disabling-secondary-dns-server-in-dd-wrt-for-opendns/>
  * <http://forums.opendns.com/comments.php?DiscussionID=6901>
  * <http://dd-wrt.com/wiki/index.php/OpenDNS>
  * <http://nixcraft.com/networking-firewalls-security/14227-allow-only-open-dns-servers-port-53-block-all-other-public-dns-servers-2.html>
