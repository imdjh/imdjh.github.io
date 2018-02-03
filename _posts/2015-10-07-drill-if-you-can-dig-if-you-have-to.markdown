---
published: true
comments: true
layout: post
title: drill if you can, dig if you have to, nslookup if you must
category: toolchain
tags: [network, migrate]
date: 2015-10-07 13:11:49 +0800
---
Although it has been 2+ years since the DNS lookup [tool chain migration][archlinux migration link] in archlinux. Many distributions still use the DNS utilities from [bind][bind homeurl] which itself was a bad idea.

__ldns__ which provides drill(1) is a fast DNS library supporting recent RFCs written in C.

Here's an interesting introduce from man page of drill(1):

>  The name drill is a pun on dig. With drill you should be able to get even more information than with dig.

Coming some of the use case of dig with the exact implements on drill:

## Lookup A record(line wrapped within double underscore is difference)

#### dig
{% highlight bash %}
$ dig djh.im
__;; <<>> DiG 9.9.5-3ubuntu0.5-Ubuntu <<>> djh.im__
__;; global options: +cmd__
__;; Got answer:__
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 28907
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

__;; OPT PSEUDOSECTION:__
__; EDNS: version: 0, flags:; udp: 512__
;; QUESTION SECTION:
;djh.im.				IN	A

;; ANSWER SECTION:
djh.im.			1799	IN	A	192.241.224.130

;; Query time: 270 msec
__;; SERVER: 8.8.8.8#53(8.8.8.8)__
;; WHEN: Wed Oct 07 02:08:36 EDT 2015
;; MSG SIZE  rcvd: __51__
{% endhighlight %}

#### drill
{% highlight bash %}
$ drill djh.im
;; ->>HEADER<<- opcode: QUERY, rcode: NOERROR, id: 61329
;; flags: qr rd ra ; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0 
;; QUESTION SECTION:
;; djh.im.	IN	A

;; ANSWER SECTION:
djh.im.	1799	IN	A	192.241.224.130

__;; AUTHORITY SECTION:__

__;; ADDITIONAL SECTION:__

;; Query time: 271 msec
;; SERVER: 8.8.8.8
;; WHEN: Wed Oct  7 02:10:13 2015
;; MSG SIZE  rcvd: 40
{% endhighlight %}

## Lookup MX record

#### dig
{% highlight bash %}
$ dig mx djh.im  # or dig -t MX djh.im
{% endhighlight %}
#### drill
{% highlight bash %}
$ drill mx djh.im  # not allowed with -t options
{% endhighlight %}

## Trace DNS lookup

#### dig
{% highlight bash %}
$ dig +trace djh.im  # with trace option, +dnssec is set
;; global options: +cmd
.			6185	IN	NS	m.root-servers.net.
.			6185	IN	NS	d.root-servers.net.
.			6185	IN	NS	i.root-servers.net.
.			6185	IN	NS	f.root-servers.net.
.			6185	IN	NS	b.root-servers.net.
.			6185	IN	NS	e.root-servers.net.
.			6185	IN	NS	h.root-servers.net.
.			6185	IN	NS	a.root-servers.net.
.			6185	IN	NS	k.root-servers.net.
.			6185	IN	NS	j.root-servers.net.
.			6185	IN	NS	c.root-servers.net.
.			6185	IN	NS	g.root-servers.net.
.			6185	IN	NS	l.root-servers.net.
.			6185	IN	RRSIG	NS 8 0 518400 20151016170000 20151006160000 62530 . how17EVioeNLHfnTfqCf+QwLlO9umhZgJq4wNA92dmIFdWFzYJ4dyllL ZII91G0p/xJIXBcbZJA9vwTBGl9iBPTj5kX70jJnTd9zGbuz9skgeeFy 5XCb0h5p+hgv5PfTwR5mcbPTOgyHenJlMLZY9Ol0Y1v3O7LcIliu90xz cI8=
;; Received 397 bytes from 8.8.8.8#53(8.8.8.8) in 58 ms

im.			172800	IN	NS	ns4.ja.net.
im.                     172800  IN      NS      barney.advsys.co.uk.
im.                     172800  IN      NS      hoppy.iom.com.
im.                     172800  IN      NS      pebbles.iom.com.
im.                     86400   IN      NSEC    immo. NS RRSIG NSEC
im.                     86400   IN      RRSIG   NSEC 8 1 86400 20151016170000 20151006160000 62530 . jjXuJc13+ymC9Kz6YL7xqx9hMVx5Bq7oIi4DBB43qYWudZVtJ9/+vkcR N5jue9G/R3FjGhUXL2WKJzOCSAE2MrXuBDR1KSnQwcCRWYfCctg8YBVm 5FAfJhaE4VEj33X2eiJcsMYHNFY2MXuCb3708PZ/q/2lX5LSYkFUTQ2W KMU=
;; Received 418 bytes from 202.12.27.33#53(m.root-servers.net) in 181 ms

djh.im.                 259200  IN      NS      ns2.digitalocean.com.
djh.im.                 259200  IN      NS      ns3.digitalocean.com.
djh.im.                 259200  IN      NS      ns1.digitalocean.com.
;; Received 105 bytes from 193.62.157.66#53(ns4.ja.net) in 151 ms

djh.im.                 1800    IN      A       192.241.224.130
;; Received 51 bytes from 198.41.222.173#53(ns3.digitalocean.com) in 68 ms
{% endhighlight %}
#### drill(with DNSSEC enabled)
{% highlight bash %}
$ drill -TD djh.im  # to compare with dig, we use -D switch by hand
Warning: No trusted keys were given. Will not be able to verify authenticity!
;; Domain: .
;; Signature ok but no chain to a trusted key or ds record
[S] . 172800 IN DNSKEY 256 3 8 ;{id = 1518 (zsk), size = 1024b}
. 172800 IN DNSKEY 257 3 8 ;{id = 19036 (ksk), size = 2048b}
. 172800 IN DNSKEY 256 3 8 ;{id = 62530 (zsk), size = 1024b}
[S] Existence denied: im. DS
;; No ds record for delegation
;; Domain: im.
;; No DNSKEY record found for im.
;; No DS for djh.im.;; No ds record for delegation
{% endhighlight %}
#### drill(without DNSSEC)
{% highlight bash %}
$ drill -T djh.im
im.	172800	IN	NS	ns4.ja.net.
im.	172800	IN	NS	hoppy.iom.com.
im.	172800	IN	NS	barney.advsys.co.uk.
im.	172800	IN	NS	pebbles.iom.com.
djh.im.	259200	IN	NS	ns3.digitalocean.com.
djh.im.	259200	IN	NS	ns1.digitalocean.com.
djh.im.	259200	IN	NS	ns2.digitalocean.com.
djh.im.	259200	IN	NS	ns3.digitalocean.com.
djh.im.	259200	IN	NS	ns1.digitalocean.com.
djh.im.	259200	IN	NS	ns2.digitalocean.com.
ns3.digitalocean.com.djh.im.	259200	IN	NS	ns3.digitalocean.com.
djh.im.	259200	IN	NS	ns1.digitalocean.com.
djh.im.	259200	IN	NS	ns2.digitalocean.com.
ns1.digitalocean.com.djh.im.	259200	IN	NS	ns3.digitalocean.com.
djh.im.	259200	IN	NS	ns1.digitalocean.com.
djh.im.	259200	IN	NS	ns2.digitalocean.com.
ns2.digitalocean.com.djh.im.	1800	IN	A	192.241.224.130
{% endhighlight %}

## Query using tcp and specified server port(line wrapped within double underscore is difference)

#### dig
{% highlight bash %}
$ dig +tcp -p 443 djh.im @208.67.220.220
__; <<>> DiG 9.9.5-3ubuntu0.5-Ubuntu <<>> +tcp -p 443 djh.im @208.67.220.220__
__;; global options: +cmd__
__;; Got answer:__
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 61923
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL:
__1__

__;; OPT PSEUDOSECTION:__
__; EDNS: version: 0, flags:; udp: 16384__
;; QUESTION SECTION:
;djh.im.                IN  A

;; ANSWER SECTION:
djh.im.         1800    IN  A   192.241.224.130

;; Query time: 367 msec
;; SERVER: 208.67.220.220__#443(208.67.220.220)__
;; WHEN: Wed Oct 07 03:43:25 EDT 2015
;; MSG SIZE  rcvd: __51__
{% endhighlight %}

#### drill
{% highlight bash %}
$ drill -t -p 443 djh.im @208.67.220.220
;; ->>HEADER<<- opcode: QUERY, rcode: NOERROR, id: 8561
;; flags: qr rd ra ; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL:
0 
;; QUESTION SECTION:
;; djh.im.  IN  A

;; ANSWER SECTION:
djh.im. 1800    IN  A   192.241.224.130

__;; AUTHORITY SECTION:__

__;; ADDITIONAL SECTION:__

;; Query time: 367 msec
;; SERVER: 208.67.220.220
;; WHEN: Wed Oct  7 03:42:31 2015
;; MSG SIZE  rcvd: 40
{% endhighlight %}

## Reverse query an IP and get minified answer

#### dig
{% highlight bash %}
$ dig +short -x 192.29.160.249
djh.im.
{% endhighlight %}

#### drill(drill has no implement in script embed options [yet][bugzilla link])
{% highlight bash %}
$ drill -x 192.241.224.130 | grep PTR | tac | head -n 1 | cut -d '	' -f5
{% endhighlight %}

## Sum up
__drill__ yet not a good replace on devops daily tool chain.
It offers less verbose output by default(but failed to dismiss empty __authorization__ and __addition__ sections). It got less options which make it useful to operators. The other side of coin is that drill offers better CLI interface which is simple, with dig, you have to put __query option__ before other __options__ which is quite annoying for beginners. __drill__ also has smaller MSG SIZE(~50 vs ~40) to send


[bind homeurl]:               https://www.isc.org/downloads/bind/
[archlinux migration link]:   https://www.archlinux.org/todo/dnsutils-to-ldns-migration/
[bugzilla link]:              https://www.nlnetlabs.nl/bugs-script/show_bug.cgi?id=550#c1
