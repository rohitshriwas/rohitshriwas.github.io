---
layout: post
title:  "Enable Two-Factor Authenticaton on Ubuntu 16.04 Xenial Xerus"
date:   2016-05-20 20:44:08 +0530
categories: ubuntu
blurb: Two-factor authentication provides unambiguous identification of users by means of the combination of two different compo...
---

<blockquote>
Two-factor authentication provides unambiguous identification of users by means of the combination of two different components. These components may be something that the user knows, something that the user possesses or something that is inseparable from the user.

-- <a href="https://en.wikipedia.org/wiki/Two-factor_authentication">Wikipedia</a>
</blockquote>

Oh, hmm. I have a phone, and I have [Google Authenticator][authenticator] on it. I also happen to have <span title="Guddii, this is your laptop :D">a laptop</span> lying around with Ubuntu on it. So why not? Here's how to do it:

* Install the Google authenticator PAM module (as root user):
{% highlight bash %}
apt-get install libpam-google-authenticator
{% endhighlight %}

* Enable the PAM module (as root user):
{% highlight bash %}
echo "auth required pam_google_authenticator.so nullok" >> /etc/pam.d/common-auth
{% endhighlight %}

* Generate a key file and initialize the thing for your user account with:
{% highlight bash %}
google-authenticator
{% endhighlight %}

* Setup a new account on the Google authenticator app on the phone with the supplied QR code / secret key from the above command.

And that should be it. Requiring the PAM module at `/etp/pam.d/common-auth` ensures TFA is applied across all authentication channels (ssh, console, and desktop at the least) while the `nullok` parameter ensures you don't end up locking yourself out if you haven't setup your account for it.

I dislike how the [instructions][github_wiki] don't necessarily elaborate on "where" exactly the `auth` line goes.
It goes in `/etc/pam.d/sshd` if you're looking to secure SSH only, right below where common-auth is included. Cheers!

[authenticator]: https://github.com/google/google-authenticator-android
[github_wiki]: https://github.com/google/google-authenticator/wiki/PAM-Module-Instructions