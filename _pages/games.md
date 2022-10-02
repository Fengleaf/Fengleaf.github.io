---
layout: archive
permalink: /games/
title: "遊戲"
author_profile: true
---

<div id="archives">
{% for category in site.categories %}
    <h3>{{category | first}}</h3>
    {{category.name}}
    {{category.title}}
    123
    <h4>456</h4>

  <!-- <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
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
  </div> -->
{% endfor %}
</div>