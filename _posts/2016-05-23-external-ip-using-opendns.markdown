---
layout: post
title:  "Getting external IP using OpenDNS"
date:   2016-05-23 11:55:16 +0530
categories: howto
---

I often need to know my external IP for debugging remote endpoints. I used to have an elaborate script to scrape it off whatismyip.com or use one of their API services, but they keep changing something or the other and make it difficult to rely on it. It turns out OpenDNS has a way to return your own IP address, and that too structured as a DNS response<sup>[source][stackex]</sup>:

{% highlight bash %}
dig @resolver2.opendns.com +short myip.opendns.com
{% endhighlight %}

That's pretty neat, it's fast and reliable :D

[stackex]: https://unix.stackexchange.com/questions/22615/how-can-i-get-my-external-ip-address-in-bash/81699#81699
