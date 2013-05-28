---
layout: post
date: 2013-05-28 18:12:45
title: Creating Static Sites in Ruby with Rack
---

There is a guide for creating static sites with rack [on the heroku dev center](https://devcenter.heroku.com/articles/static-sites-ruby), it works ok, but it has a few problems. After creating a few static sites and deploying them to heroku and taking different approaches each time, some simple, some complex, I decided to investigate the issue.

Here is the code that you might find in a lot of places around the web, including the aforementioned article:

{% highlight ruby %}
use Rack::Static, 
  :urls => ["/images", "/js", "/css"],
  :root => "public"

run lambda { |env|
  [
    200, 
    {
      'Content-Type'  => 'text/html', 
      'Cache-Control' => 'public, max-age=86400' 
    },
    File.open('public/index.html', File::RDONLY)
  ]
}
{% endhighlight %}

Firstly, `urls` can get pretty long and you mostly want to serve all files in `public` anyway, but the main issue is that all requests that don't correspond to a file will take you to `index.html`, the home page. Also, you don't really want `.html` displaying in your urls and using `File.open` in the body doesn't take advantage of all the headers that `Rack::Static` and threfore `Rack::File` will set for you.

Here's my approach. I use `Rack::Static` like so:

{% highlight ruby %}
use Rack::Static,
  :urls => Dir.glob("#{root}/*").map { |fn| fn.gsub(/#{root}/, '')},
  :root => root,
  :index => 'index.html',
  :header_rules => [[:all, {'Cache-Control' => 'public, max-age=3600'}]]
{% endhighlight %}

We get all the files in `root`, set an `index`, which will serve e.g. `about/index.html` when you get `about/` (so it also takes care of serving `/index.html` when we get `'/'`, the root) and set the `'Cache-Control'` header to an hour for all files.

After this we need to run a `rack` app. We should probably return a `404` page now, as none of the files will have been matched.

{% highlight ruby %}
headers = {'Content-Type' => 'text/html', 'Content-Length' => '9'}
run lambda { |env| [404, headers, ['Not Found']] }
{% endhighlight %}

I refactored this code into a tiny gem, so I can use it directly in all my projects. I called it [vienna](https://github.com/kmikael/vienna). Now you can create a static site on heroku by putting these two lines into your `config.ru`.

{% highlight ruby %}
require 'vienna'
run Vienna
{% endhighlight %}

(And I won't have to write any more `Rack::Static` code again.)

`vienna` will also serve a `404.html` if it exists, instead of just returning `'Not Found'` when a file isn't found. [Check out the source](https://github.com/kmikael/vienna/blob/master/lib/vienna.rb).

[pinbrowser.co](http://www.pinbrowser.co) runs on `vienna` now and I've also tested this site that is powered by `Jekyll` with `vienna`, even though I'm hosting it on GitHub Pages instead of heroku. it works great.
