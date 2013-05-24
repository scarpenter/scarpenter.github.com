---
layout: post
title: "Using Haml from the Command Line"
date: 2013-05-23 19:51
comments: false
categories:
---
Occasionally I have the need to generate some static HTML files.  They may be for a static site or just as examples of how something will look.  If the HTML is simple, I don't mind typing it out by hand.  But if there's more to it, the repetitiveness of HTML tags starts to get to me.  When I'm doing a [Rails](http://www.rubyonrails.org) or [Sinatra](http://www.sinatrarb.com) site, I use [Haml](http://haml.info) for the HTML.  So I wanted to be able to use Haml for generating static HTML as well.

<!-- more -->
The main entry point to using Haml from a command line is the [Haml::Engine](https://github.com/haml/haml/blob/master/lib/haml/engine.rb) class.  The documentation on using Haml directly like this is a little light, so I've created an example using it and made it available [on Github](https://github.com/scarpenter/command_line_haml).

In addition to just generating HTML from a Haml file, I also wanted to use some of the conveniences that are available when using Haml in a web setting.  For me, this meant being able to use a layout file as a "wrapper" around content and being able to use partials to pull out repeated code.

## Generation
The [generate.rb](https://github.com/scarpenter/command_line_haml/blob/master/generate.rb) file in my example has a fair number of comments, so I'll just hit some of the high points here.

{% codeblock Generator class lang:ruby https://github.com/scarpenter/command_line_haml/blob/master/generate.rb#L52 source %}
layout = Haml::Engine.new(File.read("./views/layout.haml"), @haml_options)
c = Context.new @example_boolean, input_file, @haml_options
output = layout.render c do
  body = Haml::Engine.new(File.read("./views/#{input_file}.haml"), @haml_options)
  body.render c
end
{% endcodeblock %}
The first step is to instantiate an instance of `Haml::Engine` pointing to the layout template.  Then we need an instance of the <a href="https://github.com/scarpenter/command_line_haml/blob/master/generate.rb#L9"><code>Context</code></a> class to provide data to the templates as well as functions that can be called from within the templates.  Next is a call to `render` on the engine instance.  We pass in the context object and a block - the block will be called when a `yield` statement is encountered in the layout template.  This is the core of what enables the use of a layout.

Inside the render block, a new `Haml::Engine` instance is instantiated pointing to the "main" template being rendered.  Then it's just a call to `render` on that instance, passing along the same `Context` instance.

## Partials
A quick word about the way I'm using partials.  I wanted the ability to render common markup on multiple pages (or render page-specific markup in the layout); the same way you would use partials on the web.
{% codeblock Context class lang:ruby https://github.com/scarpenter/command_line_haml/blob/master/generate.rb#L9 source %}
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
The implementation here is pretty simple and uses just a few conventions.  Partials live in the `partials` subfolder of the `views` folder.  The default filename for a partial is just the name of the partial.  We also check for a file prefixed with the name of the enclosing template and use that if found.  That allows us to use different content depending on the "main" template that's being rendered (check out the ["title"](https://github.com/scarpenter/command_line_haml/tree/master/views/partials) partials in the example).  If neither the template-specific nor default partial is found, `nil` is returned and nothing is rendered in the enclosing template.

{% codeblock lang:haml Layout https://github.com/scarpenter/command_line_haml/blob/master/views/layout.haml source %}
!!!
%html
  %head
    %title
      =render_partial :title
{% endcodeblock %}
Calling the partials from a template is straightforward.  One thing to note is that the name of the function (`render_partial`) is arbitrary - as long as the function exists on the `Context` object that get passed to the `render` method it can be called from within a template.

[My example](https://github.com/scarpenter/command_line_haml) certainly doesn't cover everything that could be done using Haml from the command line.  But hopefully it's enough of a start to give you some ideas.
