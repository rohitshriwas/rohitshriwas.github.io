---
layout: post
title:  "Automate Cyberoam Gateway Login w/ NetworkManager dispatcher service"
date:   2016-05-16 17:16:46 +0530
categories: linux automation cyberoam
blurb: The network at BITS Pilani Goa Campus is secured with Cyberoam UTM system and it's mandatory for all users to login to a gateway server bef...
---
The network at [BITS Pilani Goa Campus][campus] is secured with [Cyberoam][cyberoam] UTM system and it's mandatory for all users to login to a gateway server before they're allowed internet access. While this ensures fair use and quota limits for everyone, it's often a huge hassle to keep logging in whenever you reconnect. They do provide a [client][crclient] to simplify it, but I still found the manual process somewhat annoying and wasteful. So here's what I did to automate the thing (all commands as root, on Fedora 23):

* Create a new service user to run the client:
{% highlight bash %}
useradd -c "BITS / Goa Campus - Cyberoam User" -l -m -r -Z guest_u cruser
{% endhighlight %}

* Download and extract the [Cyberoam Client][crclient]:
{% highlight bash %}
cd /home/cruser/
wget http://download.cyberoam.com/solution/optionals/CyberoamLinuxClient.tar.gz
tar xf /home/cruser/CyberoamLinuxClient.tar.gz
mkdir bin && ln CyberoamLinuxClient/crclient bin/
{% endhighlight %}

* Install 32-bit libraries for the crclient binary:
{% highlight bash %}
dnf install glibc.i686 libgcc.i686
{% endhighlight %}

* Generate configuration file:<br>
<small>Change `rohitshriwas` and `wlp7s0` with your own username and network interface<small>
{% highlight bash %}
su -l cruser -c "~/bin/crclient -s -u rohitshriwas -i wlp7s0"
Server Address in dotted decimal format ::      10.1.0.10
AskonExit (y/n)?        n
AutoLogin (y/n)?        n
ShowNotification (y/n)? n
Server Address in dotted decimal format ::      10.1.0.10
Password: <type_your_password_here>
SavePassword (y/n)?     y
{% endhighlight %}

* Add a script for NetworkManager dispatcher:<br>
<small>Change `rohitshriwas` and `wlp7s0` with your own username and network interface<small>
{% highlight bash %}
cat -> /etc/NetworkManager/dispatcher.d/99-crclient << EOF
#!/bin/bash

IFACE="wlp7s0";

if [[ "$1" != "$IFACE" ]]; then
        logger "[crclient]: Not a defined interface, exiting ...";
        exit 0;
fi

if [[ "$2" = "up" ]]; then
        SSID=`nmcli -t -f active,ssid dev wifi | grep -E '^yes' | cut -d: -f2`;
        if [[ "$SSID" = "BPGC-WIFI" ]]; then
                logger "[crclient]: We're on $SSID, launching Cyberoam Client ...";
                su -l cruser -c "~/bin/crclient -l rohitshriwas";
                rm -f /tmp/CyberClient.*;
                su cruser -l -c "~/bin/crclient -u rohitshriwas -i wlp7s0 -f ~/CyberClient.conf";
        fi
fi

if [[ "$2" = "down" ]]; then
        killall -u cruser;
fi
EOF
{% endhighlight %}

* Make the script executable:
{% highlight bash %}
chmod 755 /etc/NetworkManager/dispatcher.d/99-crclient
{% endhighlight %}

And that's it! Now, I find myself already logged in whenever I reconnect to the campus Wi-FI. This can probably be refined and adapted to work with other Cyberoam enabled networks as well.

[campus]: http://www.bits-pilani.ac.in/Goa/
[cyberoam]: https://www.cyberoam.com/
[crclient]: https://www.cyberoam.com/cyberoamclients.html
