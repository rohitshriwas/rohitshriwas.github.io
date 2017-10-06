---
layout: post
title:  "CCA India Root Certificate on Linux"
date:   2017-10-05 20:47:24 +0530
categories: linux pki india
blurb: The IT Act provides for the Controller of Certifying Authorities (CCA) to license and regulate the working of Certifying Authorities and also to...
---

<blockquote>
The IT Act provides for the Controller of Certifying Authorities (CCA) to license and regulate the working of Certifying Authorities and also to ensure that none of the provisions of the Act are violated. The Certifying Authorities (CAs) issue Digital Signature Certificates(DSC) for electronic authentication of users.

-- <a href="http://www.cca.gov.in/cca/">Ministry of Electronics &amp; Information Technology, India</a>
</blockquote>

Thanks to the [IT Act][itact] and the government's [Digital India][digitalindia] campaign, we're seeing more and more digitally signed documents floating around. However, validating these signatures requires a Root Certificate which serves as the anchor from which the chain of trust for any signed electronic record is derived. Without a trusted Root available, a signed document cannot be validated.

The Root Certificate of the Controller of Certifying Authorities of India is available on [cca.gov.in][ccaindia]. While downloading and installing the certificates is a straightforward affair on Windows (Right Click -> Install), it takes a bit of a dance to do so in Linux, as described below:

* Download 'CCA India 2014' certificate from [this site][ccaindia]
* Decode the base64 encoded data with:
{% highlight bash %}
openssl base64 -d -A < cca_india_2014.cer > cca_india_2014.der
{% endhighlight %}
* Convert it from DER to PEM format with:
{% highlight bash %}
openssl x509 -in cca_india_2014.der -inform der -outform pem -out cca_india_2014.pem
{% endhighlight %}
* Print and verify certificate fingerprint[^1] with:
{% highlight bash %}
openssl x509 -in cca_india_2014.pem -fingerprint
{% endhighlight %}
* Import it to NSSDB with:
{% highlight bash %}
certutil -d /etc/pki/nssdb -A -i cca_india_2014.pem -t "C,C,C" -n "CCA India 2014"
{% endhighlight %}
* Import it to your Firefox profile with:
{% highlight bash %}
certutil -d $HOME/.mozilla/firefox/<profile_id> -A -i cca_india_2014.pem -t "C,C,C" -n "CCA India 2014"
{% endhighlight %}
* One can use `pdfsig`[^2] to validate signed PDF files:
{% highlight bash %}
pdfsig <path/to/PDF/file>
{% endhighlight %}

The 'CCA India 2015 SPL' certificate can be imported in a similar manner.

---
Footnotes:

[^1]: The correct fingerprint for verification can be obtained by sending an empty email to verifyroot@cca.gov.in, it's an automated service that responds with fingerprints and CRL.
[^2]: `pdfsig` does not support password protected documents yet.

[itact]: http://meity.gov.in/content/information-technology-act-2000
[ccaindia]: http://www.cca.gov.in/cca/?q=root_certificate.html
[digitalindia]: http://digitalindia.gov.in/
