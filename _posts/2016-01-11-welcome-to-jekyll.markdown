---
layout: post
title:  "Welcome to Jekyll!"
date:   2016-01-11 20:00:24 -0500
modified: 2016-09-16 15:04:22 -0400
categories: technology
tags: jekyll
comments: true
---

This is the first post on my new jekyll-github powered site.  I've been using [markdown][md] for a variety of writing tasks in the past few years.  It is a very natural way to write and it allows me to do all my writing in [vim][v], the same text editor I use for coding.  

I learned to touch type in 2015.  So as I'm building muscle memory for typing it makes sense to me to use the same software for all my writing.  Jekyll lets me quickly write down my thoughts in markdown format, push to github, and I'm done sharing my thoughts with the whole world.


Another nice feature:  
Jekyll also offers powerful support for code snippets:

{% highlight python %}
def print_hi(name):
  print("Hi, " + name)

print_hi('Antonios')

prints 'Hi, Antonios' to STDOUT.
{% endhighlight %}

## Setting up Jekyll
The first step was following the instructions at these two links:  

- [Github Pages](https://pages.github.com)
- [Jekyl Quick Start](https://jekyllrb.com/docs/quickstart/)

The initial install was easy enough.  However, the urls were not setup as is commonly considered best practice for SEO purposes.  Wordpress does the same thing.  Why is the default not set up for best practices?

To make the urls match the titles without any categories or dates, what is commonly referred to as 'pretty urls' I added the following line to the `_config.yml` file.

{% highlight yaml %}
permalink: :title/
{% endhighlight %}

## Comments
Jekyll does not come with commenting capabilities built in.  Many people use Disqus and I decided to do the same.  I found the documentation lacking in clarity as to how to set it up best.  Disqus site has code you can copy and paste.  There are two variables that you will need to set in this code.

{% highlight liquid %}
{% raw %}
this.page.url = "{{ site.url }}{{ page.url }}";
this.page.identifier = "{{ site.disqusid }}{{ page.url }}";
{% endraw %}
{% endhighlight %}

I set my disqus site id up in my `_config.yml` file 

{% highlight yaml %}
disqusid: replacethistextwithyourdisqusid
{% endhighlight %}

## Collections
The next addition to this site was my collection of book notes.  Since Jekyll has [collections][jd-c] that seemed like a great mechanism for creating a new series of related information like book notes.  I have to thank Ben Balter for his excellent post [Explain like I'm five: Jekyll Collections][link1]. His description was instrumental in my implementation of collections.  

The main benefit in using collections like this is that I get to keep all related writing in a subdirectory separate from regular posts or other types of writing that I might want to share.  Another benefit for readers of my site is that I can set it up so that they can easily look through related information that is available on the site.  

### What does it take to create and display collections in Jekyll?

The first step is to add the collection to your `_config.yaml` file. Below is the configuration to create a `books` collection.

{% highlight yaml %}
collections:
  books:
    output: true
{% endhighlight %}

Now throughout Jekyll I can refer to `books` just as I would `posts` and get the same result.  

The next thing to do is to create a listing page that will show all the book notes.  For this blog I simply copied the default `index.html` to `books.html` and made a few minor modifications:  

Here's what my edited `books.html` looks like:
{% highlight liquid %}
{% raw %}
---
layout: default
title: Books
---

<div class="home">
  <h1 class="page-heading">Book Notes</h1>
  <ul class="post-list">
    {% for book in site.books reversed %}
      <li>
        <span class="post-meta">{{ book.date | date: "%b %-d, %Y" }}</span>
        <h2>
          <a class="post-link" href="{{ book.url | prepend: site.baseurl }}">{{ book.title }}</a>
        </h2>
      </li>
    {% endfor %}
  </ul>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | prepend: site.baseurl }}">via RSS</a></p>
</div>
{% endraw %}
{% endhighlight %}

1. Add a title to the yaml front matter `title: Books`
2. Change the <h1> from 'Posts' to 'Book Notes'
3. Change references to `post` to `book` except for css class names.  I did not choose to change the css to look different than the posts.
4. Change the for loop to:
{% highlight liquid %}
{% raw %}
{% for book in site.books reversed %}
{% endraw %}
{% endhighlight %}

Using `reversed` shows the most recent book notes at the top of the page.

An added benefit to adding the `books.html` file next to the default `index.html` file in the root directory is that Jekyll automatically creates a menu item for each file there.  So this creates a 'Books' menu item for free!

## About page

To create an about page on my site and have it accessible via the menu, all I had to do was add an `about.md` markdown file in the root directory and Jekyll did everything else.

## Custom Url

Currently my Jekyll site is reachable from `hadji.co` or `www.hadji.co` via URL forwarding set up in my DNS. On [Github Pages][gp] I can have the site show up with my custom url by including a `CNAME` file in my github repository.  I've been debating the merits of that since I lose the https functionality that is available for free with my github account  

In other words, I can have my url show up as:  
`https://antonioshadji.github.io/`  
or  
`http://www.hadji.co/`  

Currently, Github does not offer `https` on custom domains.  I'm assuming that it is only a matter of time before they do. There are people writing about how to use cloudflare to make the site appear on `https` urls.  My understanding is that this isn't the same as having the domain secured with ssl.  Since it's not the same, I prefer to not make it appear so to visitors.

I'm choosing to continue using my `github.io` address.  I recently found some information on [Moz.com][moz] that helped me make the decision.  

<blockquote class="imgur-embed-pub" lang="en" data-id="3oFVArO"><a href="//imgur.com/3oFVArO"></a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

What caught my attention is the distinction between what happens when I use a 301 redirect vs. what happens with `<link rel=canonical ...` meta data.  

If I add a canonical url to my content in Jekyll on Github Pages the search engines will get the same redirection information that they would get with a 301 redirect.  The only difference is that users will always see Github Pages site even if the content is living elsewhere on the net someday. I'm only concerned about controlling my SEO score and this gives me that ability.  If you think I'm missing something here please let me know in the comments.  

This was also confirmed by [Yoast][y].


If you have read all the way to here, I hope that you found this information useful.  I have many plans for this site and all my Jekyll modifications will be documented in this post.  For your enjoyment and so that I can remember what I have done to modify my site from the default install.

[y]: https://yoast.com/rel-canonical/#301-redirect-vs-canonical
[moz]: https://moz.com/blog/subdomains-vs-subfolders-rel-canonical-vs-301-how-to-structure-links-optimally-for-seo-whiteboard-friday
[gp]: https://pages.github.com/
[link1]: http://ben.balter.com/2015/02/20/jekyll-collections/
[jd-c]: https://jekyllrb.com/docs/collections/
[md]: https://help.github.com/articles/github-flavored-markdown/
[v]: http://www.vim.org/
