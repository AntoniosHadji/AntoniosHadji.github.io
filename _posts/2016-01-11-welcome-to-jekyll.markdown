---
layout: post
title:  "Welcome to Jekyll!"
date:   2016-01-11 20:00:24 -0500
categories: jekyll update
comments: true
---

This is the first post on my new jekyll-github powered site.  I've been using [markdown][md] for a variety of writing tasks in the past few years.  It is a very natural way to write and it allows me to do all my writing in [vim][v], the same text editor I use for coding.  

I learned to touch type in 2015.  So as I'm building muscle memory for typing it makes sense to me to use the same software for all my writing.  Jekyll lets me quickly write down my thoughts in markdown format, push to github, and I'm done sharing my thoughts with the whole world.

Stay tuned for more content in 2016 and feel free to reach out to me if you think I can help you solve a problem.

Another feature I like:  
Jekyll also offers powerful support for code snippets:

{% highlight python %}
def print_hi(name):
  print("Hi, " + name)

print_hi('Antonios')

prints 'Hi, Antonios' to STDOUT.
{% endhighlight %}


[md]: https://help.github.com/articles/github-flavored-markdown/
[v]: http://www.vim.org/

{% if post.comments %}
<script id="dsq-count-scr" src="//antonioshadji.disqus.com/count.js" async></script>
<div id="disqus_thread"></div>
<script>
    /**
     *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
     *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables
     */
    /*
    var disqus_config = function () {
        this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
        this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() {  // DON'T EDIT BELOW THIS LINE
        var d = document, s = d.createElement('script');
        
        s.src = '//antonioshadji.disqus.com/embed.js';
        
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
{% endif %}

