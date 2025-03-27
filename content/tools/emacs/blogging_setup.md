+++
title = "My blogging setup with org-mode and ox-hugo     :spacemacs:org-mode:"
draft = true
+++

THIS IS STOLEN FROM zzamboni.org

My blogging has seen multiple iterations over the years, and with it, the tools I use have changed. At the moment I use a set of tools and workflows which make it very easy to keep my blog updated, and I will describe them in this post. In short, they are:

Writing: Emacs, org-mode
Exporting: ox-hugo
Publishing: Hugo and Netlify
Let’s take a closer look at each of the stages.

Writing with Emacs and org-mode
I have been using Emacs for almost 30 years, so its use for me is second nature. For some time I’ve been using org-mode for writing, blogging, coding, presentations and more. I am duly impressed. I have been a fan of the idea of literate programming for many years, and I have tried other tools before (most notably noweb, which I used during grad school for many of my homeworks and projects), but org-mode is the first tool I have encountered which seems to make it practical. Here are some of the resources I have found useful in learning it:

Howard Abrams’ Introduction to Literate Programming, which got me jumpstarted into writing code documented with org-mode.
Nick Anderson’s Level up your notes with Org, which contains many useful tips and configuration tricks.
Sacha Chua’s Some tips for learning Org Mode for Emacs, her Emacs configuration and many of her other articles.
Rainer König’s OrgMode Tutorial video series.
You can see some examples in my “literate config files” series, and all recent posts in this blog are written using org-mode (you can find the source file in GitLab).

Over time I have tweaked my Emacs configuration to make writing with org-mode more pleasant. You can see my Emacs configuration for reference.

So, I write posts using Emacs, in org-mode markup. What’s next?

Exporting with ox-hugo
When I first started writing my blog posts in org-mode, I relied on Hugo’s built-in support for it, which allows you to simply create posts in .org files instead of .md and have them parse in org-mode format. Unfortunately, the support is not perfect. Hugo relies on the go-org library which, while quite powerful, does not support the full org-mode markup capabilities, so many elements are not rendered or processed properly.

Happily, I discovered ox-hugo, an org-mode exporter which produces Hugo-ready Markdown files from the org-mode source, from which Hugo can produce the final HTML output. This is a much better arrangement, because each component handles only its native format: ox-hugo processes the org-mode source with the full support of org-mode and Emacs, and Hugo processes Markdown files, which are its native input format. You can use the full range of org-mode markup in your posts, and they will be correctly converted to their equivalents in Markdown. Furthermore, your source files remain output-agnostic, as you can still use all other org-mode exporters if you need to produce other formats.

Ox-hugo supports two ways of organizing your posts: one post per org file, and one post per org subtree. In the first one, you write a separate org file for each post. In the second, you keep all your posts in a single org file, and specify (through org-mode properties) which subtrees should be exported as posts. The latter is the recommended way to organize posts. At first I was skeptical - who wants to keep everything in a single file? However, as I have worked more with it, I have come to realize its advantages. For one, it makes it easier to specify post metadata - for example, I have defined sections in my org-mode source file for certain frequent topics, and those are tagged accordingly in the org source. When I create posts as subtrees of those sections, they inherit the top-level tags automatically, as well as any other properties, which I use, for example, to define the header images used in the posts. Having all posts in a single file also makes it easier to share other content, such as org macro definitions, ox-hugo configuration options, etc.

Note that ox-hugo is not limited to exporting blog posts, but any content processed by Hugo. For example, my org source file also includes all the static pages in my web site - they are differentiated from blog posts simply by the Hugo section to which they belong, which is defined using the HUGO_SECTION property in my Org file.

Since the full power of org markup is available when using ox-hugo, you can do very interesting things. For example, all the posts in my Literate Config Files category are automatically updated every time I export them with the actual, real content of the corresponding config file, which I also keep in org format. There is a lot of hidden power in org-mode and ox-hugo. My recommendation is to go through the source files for some of the websites listed in ox-hugo’s Real World Examples section. I have learned a lot by reading through the source files for the ox-hugo website itself.

Once you have some contents in your Org file, you can export them into Markdown files. For this, use the standard Org export dispatcher (bound to C-c C-e by default) and choose [H] Export to Hugo-compatible Markdown / [A] All subtrees (or File) to Md file(s) options (you may choose other options for course, but this one exports the whole file). Ox-hugo knows the default structure expected by Hugo (a top-level content/ directory in which you have directories for each section), so there’s usually not much to do other than point ox-hugo to where your top-level Hugo directory is, using the HUGO_BASE_DIR property.

This presumes you already have a Hugo site created. If you have never used Hugo before, I would suggest you go through the Quick Start guide, which shows how to set up a basic website using the Ananke theme. This is the same theme I use for my website (with some modifications).
Hugo has extensive capabilities and it is beyond the scope of this article to show you how to use it, but it has very good documentation which I would urge you to peruse to learn more about it. Feel free to peruse my setup for ideas.

Normally I run hugo locally to make sure the export is OK, particularly when I’m tweaking with my sites’ theme or settings. To do this, you can simply run:

hugo server
And browse to <http://localhost:1313>.

Publishing with Hugo and Netlify
Finally! Once you are happy with the results, we have come to the point of publishing the website. I used GitHub Pages for a long time, but nowadays I use Netlify, which does a great job of hosting websites. After connecting Netlify to my website’s GitLab repository, all I have to do is push the files, and Netlify takes care of running Hugo on them and publishing the results. Netlify even handles the DNS records and SSL certificates for my domain!

Netlify has impressive capabilities, but for a basic website like mine, a mostly default setup works well. This is what the Build Settings look like:

Note that I change Hugo’s publishDir parameter from its default value of public to docs, and configure Netlify to match (note that this could also be configured in netlify.toml, below). This is done by specifying the parameter in Hugo’s config.toml file:

  publishDir = "docs"
My repository contains a netlify.toml file which is used to configure some Hugo environment variables, and to specify the version of Hugo to use:

[build.environment]
  HUGO_ENV = "production"
  HUGO_VERSION = "0.83.1"
I also keep an Elvish script for automatically updating this file to the version of Hugo currently installed on my laptop. Whenever I update Hugo locally, I test my website using hugo server, and then run this to instruct Netlify to upgrade to the latest version as well:

\#!/usr/local/bin/elvish
use re
hugo-ver = (put (re:find 'v([\d.]+)' (hugo version))[groups][1][text])
hugo-env = "production"
echo '[build.environment]
  HUGO_ENV = "'$hugo-env'"
  HUGO_VERSION = "'$hugo-ver'"' &gt; netlify.toml
git add netlify.toml
git ci -m 'Updated Hugo version to '$hugo-ver
git push
Finally, Hugo aliases can be handled via Netlify redirects by following the instructions from this blog post to automatically populate the redirects configuration from the Hugo source files.

Conclusion
That’s it! With this setup, I can write all the contents for my website in Org-mode, and the rest is handled automatically by the tools. This makes it very easy to keep my website updated. And all these tools are available for free!

I hope you find this useful. Let me know in the comments if you have any questions.
