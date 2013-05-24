---
layout: post
date: 2013-05-24 14:52:10
title: Hello, Jekyll
---

I'm launching this site today. It's a static site built with [Jekyll](http://jekyllrb.com) and the color scheme and choice of fonts has been havily inspired by [orderedlist's minimal theme](https://github.com/orderedlist/minimal). I'm not going to talk about building a site with Jekyll, because the newly launched [official site](http://jekyllrb.com) does an excellent job at that, but I am going to talk about a few problems I had along the way and a few things you can do to improve your Jekyll workflow.

## Use a Rakefile

It gets boring to type `jekyll serve --watch` and to create new files named `2013-05-24-hello-jekyll.md` really fast. A few `rake` tasks can significantly speed things up. This is what mine looks like:

{% highlight ruby %}
task :default => [:serve]

desc 'Build and watch the site'
task :build do
  system 'jekyll build --watch'
end

desc 'Serve and watch the site'
task :serve do
  system 'jekyll serve --watch'
end

desc 'Create a new post'
task :post do
  date = Time.new
  title = STDIN.gets.chomp
  
  content = <<-EOS.gsub(/^ +/, '')
    ---
    layout: post
    date: #{date.strftime('%Y-%m-%d %H:%M:%S')}
    title: #{title}
    ---
    
  EOS
  
  datef = date.strftime('%Y-%m-%d')
  titlef = title.downcase.gsub(' ', '-')
  filename = "_posts/#{datef}-#{titlef}.md"
  
  File.write filename, content
end
{% endhighlight %}

## Use rdiscount or redcarpet

The default markdown parser `maruku` works fine most of the time but I've run into a few problems like getting an `REXML could not parse this XML/HTML` error when I try to highlight Objective-C code. Switch to `rdiscount` or `redcarpet` and the error goes away. Just add `markdown: redcarpet` to your cofiguration file.

## Pretty Permalinks

Add `permalink: pretty` to your configuration file if you want posts to be served from `/2013/05/24/hello-jekyll/` instead of `/2013/05/24/hello-jekyll.html`. This also affects pages, so `about.html` will be available at `/about/`.

## Using Pow

If you want to use the `rack` server [pow](http://pow.cx), build your site into `public` instead of `_site`, because `pow` automatically serves everything in `public` as static assets.

## An RSS Feed

You'll probably want to have an RSS feed for your site, I've described how I built one [in a seperate post](/2013/05/23/adding-an-rss-feed-to-a-jekyll-site/).
