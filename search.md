---
layout: page
title: Search
permalink: /search/
---

<div class="home">

  <div id="search-searchbar"></div>

  <div class="post-list" id="search-hits">
    {% for post in site.posts %}
      <div class="post-item">
        {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
        <span class="post-meta">{{ post.date | date: date_format }}</span>

        <h2>
          <a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a>
        </h2>
        <div class="post-snippet">{{ post.excerpt }}</div>
      </div>
    {% endfor %}
  </div>

  <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>

</div>

{% include algolia.html %}