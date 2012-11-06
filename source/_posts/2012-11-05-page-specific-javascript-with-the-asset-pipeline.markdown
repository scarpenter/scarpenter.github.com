---
layout: post
title: "Page Specific Javascript With the Rails Asset Pipeline"
date: 2012-11-05 16:37
comments: false
categories: 
---
Rails' asset pipeline is a great way to manage your javascript and CSS in a Rails app.  By default it handles pre-processing your files (converting CoffeeScript to javascript and SASS to CSS), concatentating your files into a single file, and generating filenames including hashes of the content.  This reduces the number of HTTP requests necessary to download your files and makes it easy to use HTTP caching on your assets.

<!-- more -->

This works fine by default if you reference all of your javascript files from `application.js` which serves as a manifest of included files.  In the development environment the files are served individually and un-minified (which is useful for debugging) and in production they are concatenated and minified.  But what do you do if you want to have page (or section)-specific files?

Your first approach might be to just add a new file to the `app/assets/javascripts/` folder in your project and then reference it from your page:

<div class="centered screenshots">
    {% img http://pictures.seancarpenter.net/blog/assets_first.png 'assets directory' 'assets directory' %}
    <em>A separate CoffeeScript file</em>
</div>

{% codeblock lang:ruby %}
<!-- Other header-type stuff -->
<%= javascript_include_tag "separated_code" %>
{% endcodeblock %}

This will work fine in development.  The file will be pre-processed and served separately like all the others and life will be good.  The problem is that this will fail in production if you're precompiling your assets (which you should be).  The reason is that only `application.js` is considered as a manifest during precompilation.  So while `separated_code.js.coffee` exists in the assets directory it won't be compiled and minified for production.

You can add additional manifest files to the list considered during precompilation fairly simply by modifying `application.rb`.  You can modify the `precompile` property of the assets configuration like so:

{% codeblock application.rb lang:ruby %}
config.assets.precompile += %w( separated.js )
{% endcodeblock %}

And add a new manifest file that `requires` the actual code:

{% codeblock separated.js lang:javascript %}
//= require separated_code
{% endcodeblock %}

This works but it's pretty tedious to add every new file like this.  So instead, I decided to make it more generic.  I modified my `precompile` property like this:

{% codeblock application.rb lang:ruby %}
config.assets.precompile += %w( *-bundle.js )
{% endcodeblock %}

Now I can add as many additional javascript manifest files as I need and include whatever individual files in those that I want to.  I've been using directories to organize the files that go in each bundle.  For example:

<div class="centered screenshots">
    {% img http://pictures.seancarpenter.net/blog/assets_grouped.png 'assets directory' 'assets directory' %}
    <em>CoffeeScript files for a section</em>
</div>

{% codeblock section1-bundle.js lang:javascript %}
//= require_tree ./section1/
{% endcodeblock %}

And in my actual page:
{% codeblock lang:ruby %}
<!-- Other header-type stuff -->
<%= javascript_include_tag "section1-bundle" %>
{% endcodeblock %}

One additional step is to remove the `require_tree` directive from `application.js`.  Otherwise (since `require_tree` is recursive) the files in each bundle will also be included in the main application file which isn't what you want.

Now I'm free to organize my javascript files in whatever way makes sense but still get the advantages of the asset pipeline (precompliation/concatentation/minification).  I've done a similar thing with my CSS files (although I've found there's less reason to separate my CSS as much as my javascript).

I try to avoid putting anything that is shared into more than one of these bundles.  Placing the same file in more than one bundle will result in the contents of that file being downloaded more than once since it will be concatenated with the rest of the files in each bundle.  So I try to keep shared stuff required from `application.js`.
