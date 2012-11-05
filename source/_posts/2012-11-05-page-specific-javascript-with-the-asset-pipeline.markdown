---
layout: post
title: "Page Specific Javascript With the Rails Asset Pipeline"
date: 2012-11-05 16:37
comments: false
published: false
categories: 
---
Rails' asset pipeline is a great way to manage your javascript and CSS in a Rails app.  In a nutshell, it handles pre-processing your files (converting CoffeeScript to javascript and SASS to CSS), concatentating your files into a single file, and generating filenames including hashes.  This is good since you can then set far-future expires headers on your assets to get good caching behavior.

<!-- more -->

It all works good if you reference all of your javascript files from `application.js` which serves as a manifest for javascript files to include.  In development the files are served individually (which is useful for debugging) and in production they are concatenated and minified.  But what do you do if you want to have page (or section)-specific files?

Your first approach might be to just add a new javascript file to the `app/assets/javascripts/` folder in your project and then reference it from your page:
__include screenshot of files__

{% codeblock lang:ruby %}
# Other header-type stuff
= javascript_include_tag "separated_code"
{% endcodeblock %}

The problem here is that it will work fine in development.  The file will be served separately like all the others and life will be good.  The problem is that this will fail in production if you're precompiling your assets (which you should be).  The reason is that only `application.js` is considered during precompilation.  So while `separated_code.js.coffee` exists in the assets directory it won't be compiled and minified for production.

You can add additional files to the list considered during precompilation, you need to modify `application.rb`.  You can modify the `precompile` property of the assets configuration like so:
__make sure this part actually works__

{% codeblock application.rb lang:ruby %}
config.assets.precompile += %w( separated_code.js.coffee )
{% endcodeblock %}

This works but it's pretty tedious to add every new file like this.  So instead, I decided to make it more generic.  I modified my `precompile` property like this:

{% codeblock application.rb lang:ruby %}
config.assets.precompile += %w( *-bundle.js *-bundle.css )
{% endcodeblock %}

Now I can add as many additional javascript and CSS manifest files as I need and include whatever individual files in those that I want to.  I've been using directories to organize the files that go in each bundle.  For example:
__include screenshot of app/assets/javascripts/__

{% codeblock section1.js lang:javascript %}
//= require section1/main.coffee
//= require section2/other.coffee
{% endcodeblock %}

And in my actual page:
{% codeblock lang:ruby %}
#Other header stuff
= javascript_include_tag "section1"
{% endcodeblock %}

Now I'm free to organize my javascript and CSS files in whatever way makes sense but I still get the advantages of the asset pipeline (precompliation/concatentation/minification).

Mention something about the tradeoffs involved here - you can have section-specific stuff but anything shared between multiple sections will be downloaded more than once.
