---
layout: post
title: "Scraping Web Pages with Ruby"
date: 2013-01-26 17:00
comments: false
categories:
---
Ideally every site or service that you wanted to get information from in an automated fashion would provide an API.  But until that becomes a reality, page scraping is a useful technique to have in your toolbox.

<!--more -->

## Basic Scraping
Basic scraping can be done by simply requesting the page and looking through the returned string for the information you want.  For simple data extraction, that may be all you need.  But if the data you're looking for is harder to find, a library that provides some structured ways to access the data is more appropriate.

One such library in Ruby is [Nokogiri](http://nokogiri.org).  Although I usually think of Nokogiri as an XML parser, it also handles HTML.  Grabbing a page and finding some text is a few simple commands away:
{% codeblock lang:ruby %}
require "nokogiri"
require "open-uri"

page = Nokogiri::HTML(open("http://seancarpenter.net"))

# Nokogiri supports CSS-style selectors
h2s = page.css("#left h2")
puts h2s.length # outputs "3"

# And XPath selectors
h2s_xpath = page.xpath("//div[@id='left']/h2")
puts h2s_xpath.length # outputs "3"

{% endcodeblock %}
If reading data is the only thing you need to do, then Nokogiri gets the job done.

## Reading and Sending Data
Sometimes you may need to send some data in addition to just reading it.  One common situation is needing to log in to a site before being able to read the data you're interested in.  In that case, another library comes to the rescue: [Mechanize](http://mechanize.rubyforge.org/).

Mechanize can handle all of the "bookkeeping" aspects of logging in: setting and sending cookies, following redirects, etc.  Mechanize also provides methods for dealing with [links](http://mechanize.rubyforge.org/Mechanize/Page/Link.html), [forms](http://mechanize.rubyforge.org/Mechanize/Form.html), and other structural elements of a page.  And when it comes time to extract the data, Mechanize uses Nokogiri under the hood so that you can use the same CSS and XPath selectors that Nokogiri provides.

Mechanize provides some nice [examples](http://mechanize.rubyforge.org/EXAMPLES_rdoc.html) in their documentation so I won't repeat them here.  Definitely check it out if you need to do any more "advanced scraping".

While someday there may be an API available to get at any data that anyone finds interesting, until then don't discount scraping as a viable alternative.
