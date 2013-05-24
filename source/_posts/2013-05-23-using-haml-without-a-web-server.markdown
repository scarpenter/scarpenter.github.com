---
layout: post
title: "Using Haml without a Web Server"
date: 2013-05-23 19:51
comments: false
categories:
---
Occasionally I have the need to generate some static HTML files.  They may be for a static site or just as examples of how something may look.  If the HTML is simple, I don't mind typing it out by hand.  But if there's more to it, the repitiveness starts to get to me.  When I'm doing a [Rails](http://www.rubyonrails.org) or [Sinatra](http://www.sinatrarb.com) site, I would use [Haml](http://haml.info) for the HTML.  So I wanted to be able to use Haml for static HTML as well.

<!-- more -->
The main entry point to using Haml from a command line is the [Haml::Engine](https://github.com/haml/haml/blob/master/lib/haml/engine.rb) class.  The documentation on using Haml directly like this is a little light, so I've created an [example on Github](https://github.com/scarpenter/command_line_haml).

I wanted to be able to use some basic partials functionality like you would find in a web context. Also layouts.

The [Generator](https://github.com/scarpenter/command_line_haml/blob/master/generate.rb) class is my example has a fair number of comments, so I'll just hit some of the high points here.

{% codeblock Generator class lang:ruby https://github.com/scarpenter/command_line_haml/blob/master/generate.rb Source %}
layout = Haml::Engine.new(File.read("./views/layout.haml"), @haml_options)
c = Context.new @example_boolean, input_file, @haml_options
output = layout.render c do
  body = Haml::Engine.new(File.read("./views/#{input_file}.haml"), @haml_options)
  body.render c
end
{% endcodeblock %}
The first step is to instantiate an instance of `Haml::Engine` pointing it to the layout template.  Then we need an instance of the `Context` class to provide data to the templates as well as functions that can be called from within the templates.  Next is a call to `render` on the engine instance pointing to the layout template.  We pass in the context object and a block - the block will be called when a `yield` statement is encountered in the layout template.

## Partials
A quick word about the way I'm using partials.  I wanted the ability to render common markup on multiple pages (or render page-specific markup in the layout); the same way you would use partials on the web.
{% codeblock Context class lang:ruby https://github.com/scarpenter/command_line_haml/blob/master/generate.rb Source %}
def render_partial(file_name)
  file_to_render = "./views/partials/#{file_name.to_s}.haml"
  if @scope
    scope_file = "./views/partials/#{@scope.to_s}_#{file_name.to_s}.haml"
    file_to_render = scope_file if File.exists? scope_file
  end
  if File.exists? file_to_render
    partial = Haml::Engine.new(File.read(file_to_render), @options)
    partial.render
  else
    nil
  end
end
{% endcodeblock %}
The implementation here is pretty simple and uses just a few conventions.  Partials live in the `partials` subfolder of the `views` folder.  The default template file for a partial is just the name of the partial.  We also check for a file prefixed with the name of the enclosing template and use that if found.  That allows us to use different content depending on the "main" template that's being rendered (check out the "title" partials in the example).  If neither the template-specific nor default partial is found, `nil` is returned and nothing is rendered in the enclosing template.
