---
layout: post
title: "Using Passenger Standalone in an Apache Location Element"
date: 2012-10-22 8:39
comments: false
published: false
categories: 
---
Using [Passenger Standalone](http://www.modrails.com/documentation/Users%20guide%20Standalone.html) and [RVM](https://rvm.io) you can run web apps using multiple versions of Ruby on a single server. Phusion has a [blog post](http://blog.phusion.nl/2010/09/21/phusion-passenger-running-multiple-ruby-versions/) with fairly detailed instructions on getting this set up.

One assumption in that post is that each web app will be running as its own site (using the ServerName directive inside an Apache [&lt;VirtualHost&gt;](http://httpd.apache.org/docs/2.2/mod/core.html#virtualhost) element). In my case, I wanted to run each web app as subdirectories in a single &lt;VirtualHost&gt;. So my original configuration (with everything running the same Ruby version) looked like this:
<!-- more -->

{% codeblock lang:aconf %}
<VirtualHost *:80>
    # Other configuration directives...
    RackBaseURI /app1
    RackBaseURI /app2
    RackBaseURI /app3
</VirtualHost>
{% endcodeblock %}

This server is using (regular) Passenger to serve 3 Rack apps at the urls /app1, /app2, and /app3. Now I want to run app1 under a different Ruby version. Following the instructions in Phusion's post, I installed Ruby 1.9.3 using RVM and then installed Passenger (the `--pre` flag isn't necessary for installing Passenger anymore since the current release version includes the Standalone server).

The next step is to switch app1 to Ruby 1.9.3 while leaving app2 and app3 alone:

{% codeblock lang:aconf %}
<VirtualHost *:80>
    # Other configuration directives...
    RackBaseURI /app2
    RackBaseURI /app3
    <Location /app1>
        PassengerEnabled off
        ProxyPass http://127.0.0.1:3000/
        ProxyPassReverse http://127.0.0.1:3000/
    </Location>
</VirtualHost>
{% endcodeblock %}

There are only a few changes here. First, app1 has its `RackBaseURI` directive removed. Secondly, a &lt;Location&gt; element was added for the `/app1` virtual directory. On line 6 the `PassengerEnabled` directive turns off the "main" Passenger processing for this virtual directory. Then the `ProxyPass` and `ProxyPassReverse` directives are added to handle the proxying to Passenger Standalone.

With only those few changes, app1 is now running on Ruby 1.9.3 while app2 and app3 continue to run as they did before. If the need arises later to run another version of Ruby it's just a matter of installing it with RVM, setting up a new instance of Passenger Standalone, and adding another &lt;Location&gt; element.
