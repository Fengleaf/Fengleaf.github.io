---
layout: archive
permalink: /games/
title: "遊戲"
author_profile: true
---

<div id="archives">
{% for category in site.categories %}
    {% capture category_name %}{{ category | first }}{% endcapture %}
    {% if category_name == "遊戲" %}
        {% for post in site.categories[category_name] %}
        <li>
        <article class="archive-item">
          <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
        </article>
        </li>
        {% endfor %}
    {% endif %}
{% endfor %}
</div>