---
layout: post
title:  "Welcome to Jekyll!"
date:   2016-01-11 20:00:24 -0500
categories: jekyll update
comments: true
---

This is the first post on my new jekyll-github powered site.  I've been using [markdown][md] for a variety of writing tasks in the past few years.  It is a very natural way to write and it allows me to do all my writing in [vim][v], the same text editor I use for coding.  

I learned to touch type in 2015.  So as I'm building muscle memory for typing it makes sense to me to use the same software for all my writing.  Jekyll lets me quickly write down my thoughts in markdown format, push to github, and I'm done sharing my thoughts with the whole world.


Another feature I like:  
Jekyll also offers powerful support for code snippets:

{% highlight python %}
def print_hi(name):
  print("Hi, " + name)

print_hi('Antonios')

prints 'Hi, Antonios' to STDOUT.
{% endhighlight %}

## Setting up Jekyll
The first step was following the instructions at these two links:
- https://pages.github.com
- https://jekyllrb.com/docs/quickstart/

The initial install was easy enough.  However, the urls were not setup as is commonly considered best practice for SEO purposes.  Wordpress does the same thing.  Why is the default not set up for best practices?

To make the urls match the titles without any categories or dates, what is commonly referred to as 'pretty urls' I added the following line to the `_config.yml` file.

{% highlight yaml %}
permalink: :title/
{% endhighlight %}

Jekyll does not come with commenting capabilities built in.  Many people use Disqus and decided to do the same.  I found the documentation lacking in clarity as to how to set it up best.  Disqus site has code you can copy and paste.  There are two variables that you will need to set in this code.

{% highlight liquid %}
{% raw %}
this.page.url = "{{ site.url }}{{ page.url }}";
this.page.identifier = "{{ site.disqusid }}{{ page.url }}";
{% endraw %}
{% endhighlight %}

I set my disqus site id up in my `_config.yml` file 

{% highlight yaml %}
disqusid: antonioshadji
{% endhighlight %}

Stay tuned for more content in 2016 and feel free to reach out to me if you think I can help you solve a problem.

[md]: https://help.github.com/articles/github-flavored-markdown/
[v]: http://www.vim.org/
