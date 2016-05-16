---
layout: post
title:  "Installing Python 3 with virtualenv on CentOS 7"
date:   2016-05-11 14:52:16 +0530
categories: centos python
---
It's 2016 and CentOS 7 is still defaulting to Python 2.7! Thankfully Python 3 has landed in the EPEL repo and here's how I got it working with virtualenv on a fresh install of CentOS 7:

* Enable the EPEL repository with:
{% highlight bash %}
yum install epel-release
{% endhighlight %}

* Install python34 and python-pip packages with:
{% highlight bash %}
yum install python34 python-pip
{% endhighlight %}

* Update pip and install virtualenv with:
{% highlight bash %}
pip install -U pip
pip install virtualenv
{% endhighlight %}

* Create and activate a new python3 virtualenv with:
{% highlight bash %}
virtualenv -p python3 <env_name>
{% endhighlight %}

* Activate the environment and code away :D
{% highlight bash %}
cd <env_name> && source bin/activate
{% endhighlight %}
