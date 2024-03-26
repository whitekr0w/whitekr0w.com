---
layout: default
title: Home
---

Hi, my name is **Shane**, also known by my alias as **whitekr0w**. This is my blog that has a bunch of stuff about me, what I do, and what I've done. My goal for this blog is to write something that "helps the next guy". I've spent many hours sifting through countless books, manuals, and Google pages to answer questions I've encountered that just didn't have answers. My goal for this blog is to showcase my findings and experiences so that I can help someone else down the line. <br><br>

{% for post in site.posts limit:1 %}
# Featured Post
<h1 class="post-title">
  <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
</h1>
<time datetime="{{ post.date | date_to_xmlschema }}" class="post-date">{{ post.date | date_to_string }}</time>
  {{ post.excerpt }}
  <a href="{{ site.baseurl }}{{ post.url }}">Read more</a>
{% endfor %}

# Latest Posts
{% for post in site.posts offset:1 limit:4 %}
- <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
{% endfor %}