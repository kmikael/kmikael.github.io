---
layout: post
date: 2013-05-23 00:00:00
title: Adding An RSS Feed to a Jekyll Site
---

Adding an RSS 2.0 feed to a Jekyll site is actually pretty simple.

First the required *header* and *footer*:

{% highlight xml %}
{% raw %}
<?xml version="1.0" encoding="utf-8"?>
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <atom:link href="{{ site.url }}/feed.xml" rel="self" type="application/rss+xml" />
    <title>{{ site.name }}</title>
    <link>{{ site.url }}</link>
    <description>{{ site.description }}</description>
  </channel>
</rss>
{% endraw %}
{% endhighlight %}

Don't forget to add a *name*, *url* and *description* to your configuration file.

{% highlight yaml %}
url: http://kmikael.com

name: Mikael Konutgan
description: 'Thoughts on programming, Unix and technology'
{% endhighlight %}
    
We've got our XML boilerplate and then in the `channel` we need to add a `title`, a `link` and a `description`. The `channel` elements also has [a few optional elements](http://feed2.w3.org/docs/rss2.html#optionalChannelElements), which I haven't included here.

The next step is to add the `item`'s. We iterate over the 20 latest posts and add them. Each `item` has a `title`, a `link` a `guid` (globally unique identifier), a `pupDate` and a `description`. The `guid` can be any string, here I just set it to the link of the post. [`isPermaLink="true"` tells feed readers that they can assume the `guid` is a permalink](http://feed2.w3.org/docs/rss2.html#ltguidgtSubelementOfLtitemgt).

Actually non of these are required, it's the most minimal approach that makes sense in my opinion. [To quote the spec](http://feed2.w3.org/docs/rss2.html#hrelementsOfLtitemgt):

> An item may also be complete in itself, if so, the description contains the text (entity-encoded HTML is allowed), and the link and title may be omitted. All elements of an item are optional, however at least one of title or description must be present.

We use the `date_to_rfc822` and `xml_escape` filters Jekyll adds to Liquid to comply with [the specification](http://feed2.w3.org/docs/rss2.html#ltpubdategtSubelementOfLtitemgt).

{% highlight xml %}
{% raw %}
{% for post in site.posts limit:20 %}
  <item>
    <title>{{ post.title }}</title>
    <link>{{ site.url }}{{ post.url }}</link>
    <guid isPermaLink="true">{{ site.url }}{{ post.url }}</guid>
    <pubDate>{{ post.date | date_to_rfc822 }}</pubDate>
    <description>{{ post.content | xml_escape }}</description>
  </item>
{% endfor %}
{% endraw %}
{% endhighlight %}

After we add the YAML front-matter, so Liquid will process our file, the end result looks like this:

{% highlight xml %}
{% raw %}
---
layout: nil
---

<?xml version="1.0" encoding="utf-8"?>
<rss version="2.0">
  <channel>
    <title>Mikael K.</title>
    <link>{{ site.url }}</link>
    <description>Thoughts on programming and technology</description>
    {% for post in site.posts limit:20 %}
      <item>
        <title>{{ post.title }}</title>
        <link>{{ site.url }}{{ post.url }}</link>
        <guid isPermaLink="true">{{ site.url }}{{ post.url }}</guid>
        <pubDate>{{ post.date | date_to_rfc822 }}</pubDate>
        <description>{{ post.content | xml_escape }}</description>
      </item>
    {% endfor %}
  </channel>
</rss>
{% endraw %}
{% endhighlight %}

See [the RSS 2.0 specification](http://feed2.w3.org/docs/rss2.html) for all of the details and optional elements and [validate your feed](http://validator.w3.org/feed/) when you're done. Using the method above should produce a valid feed without warnings.
