---
layout: post
title: "CPOSC 2013 Presentation Notes"
date: 2013-10-20 10:05
comments: false
categories:
---
Thanks to everyone who came out to see my presentation at [CPOSC](http://cposc.org) yesterday.

<!-- more -->

I've posted a [copy of my slides](http://www.slideshare.net/stcarpenter/beyond-heroku-hosting-your-ra) on SlideShare.  Here's a list of resources that I've used in setting up nginx, Unicorn, and Capistrano.

### Sample Project
Here's the [Github repository](https://github.com/scarpenter/cposc) for the small example I showed at the end of the presentation.  In the <code>sample_config_files</code> directory there are a few files that are not actually part of the Rails project but instead are config files that would be found on the server:
<ul class="indented-list">
	<li>nginx_site.conf: This is an example of the nginx configuration used to proxy requests to Unicorn</li>
	<li>unicorn_init: I didn't talk about this in the presentation, but this is an example of a file that can be placed in <code>/etc/init.d</code> and used to start Unicorn on machine startup</li>
	<li>cposc.conf: This is the project-specific configuration file used by the above <code>unicorn_init</code>.</li>
</ul>

### Unicorn
<ul class="indented-list">
	<li><a href="http://unicorn.bogomips.org">Unicorn home page</a></li>
	<li><a href="http://sirupsen.com/setting-up-unicorn-with-nginx/">A blog post by Simon Hørup Eskildsen</a> on setting up Unicorn and nginx.  Good general information.</li>
	<li><a href="http://matteodepalo.github.io/blog/2013/03/07/how-i-migrated-from-heroku-to-digital-ocean-with-chef-and-capistrano/">A blog post from Matteo Depalo</a> on migrating from Heroku to Digital Ocean.  This helped inspire me to do this myself.  Matteo's post talks about using Chef to set up your server in addition to the Unicorn/nginx/Capistrano combination.  My personal choice is to use <a href="http://www.ansibleworks.com/docs/">Ansible</a> for configuration management.
	<li><a href="https://github.com/blog/517-unicorn">Github's post</a> about switching to Unicorn.</li>
	<li><a href="https://github.com/defunkt/unicorn/tree/master/examples">Github's "unofficial" Unicorn source code mirror</a>. This <code>examples</code> directory has sample configuration files for Unicorn, nginx, and logrotate with lots of comments.</li>
</ul>

### nginx
<ul class="indented-list">
	<li><a href="http://nginx.org">nginx home page</a></li>
	<li><a href="http://nginx.org/en/docs/">nginx config documentation</a></li>
	<li><a href="http://nginx.org/en/docs/http/ngx_http_upstream_module.html">Documentation on the upstream configuration</a> used to reverse proxy to Unicorn.</li>
</ul>

### Capistrano
Capistrano recently released a new version (3.0) that makes some changes to how Capistrano is configured and runs.  I've only used the 2.x version and that's what I covered in my presentation.  The [Capistrano website](http://www.capistranorb.com) has information on the new version; I've used the [Github wiki](https://github.com/capistrano/capistrano/wiki/2.x-Getting-Started) when setting up version 2.x.

### Hosts
These are the Linux hosts I mentioned during my presentation; there are certainly a lot more.
<ul class="indented-list">
	<li><a href="http://aws.amazon.com">Amazon AWS</a></li>
	<li><a href="http://www.windowsazure.com">Windows Azure</a></li>
	<li><a href="https://www.digitalocean.com/?refcode=a4c745925e6a">Digital Ocean</a></li>
	<li><a href="https://www.linode.com/?r=fdd2b3b2ca8d34a683ac7b27827b59f8150464b5">Linode</a></li>
</ul>

### Summary
Thanks to all of the other speakers and the conference organizers for an enjoyable day.  I also had a fun lunch discussion with Scott, Stefan, Samrat, Hector, Jeff, and Nathan (sorry if I missed anyone!).

If you have any questions about my presentation, feel free to reach out on [Twitter](https://twitter.com/scarpenter) or by [email](mailto:sean@seancarpenter.net).
