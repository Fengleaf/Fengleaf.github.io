---
title: "作品集"
layout: archive
excerpt: "作品集"
sitemap: true
permalink: /works/
author_profile: true
toc: true
---
內容目前正在施工中
{: .notice--danger}

<div id="archives">
{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    {% if category_name != "遊戲" %}
      <div id="#{{ category_name | slugize }}"></div>
      <h3 class="category-head">{{ category_name }}</h3>
      <a name="{{ category_name | slugize }}"></a>
      <ul>
      {% for post in site.categories[category_name] %}
      <li>
      <article class="archive-item">
        <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
      </article>
      </li>
      {% endfor %}
      </ul>
    {% endif %}
  </div>
{% endfor %}
</div>