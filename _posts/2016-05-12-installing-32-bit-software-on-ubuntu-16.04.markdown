---
layout: post
title:  "Installing 32-bit libraries on Ubuntu 16.04 Xenial Xerus"
date:   2016-05-12 08:48:59 +0530
categories: ubuntu
---
Ubuntu used to have a hackish way to get a 32-bit runtime on a 64-bit system with the _ia32-libs_ package. However, that has been deprecated in favour of [MultiArch][multiarch] and now we have the following steps to get things like [adb][adb] and [crclient][crclient] working again:

* Enable the i386 architecture (as root user):
{% highlight bash %}
dpkg --add-architecture i386
apt-get update
{% endhighlight %}

* Install 32-bit libraries (as root user):
{% highlight bash %}
apt-get install libc6:i386 libstdc++6:i386
{% endhighlight %}

It's worth noting how it's similar to how it's done in Fedora, i.e. `dnf install glib6.i686` and so on.

[multiarch]: https://help.ubuntu.com/community/MultiArch
[adb]: https://developer.android.com/tools/help/adb.html
[crclient]: http://www.cyberoam.com/cyberoamclients.html
