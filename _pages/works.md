---
title: "作品集"
layout: single
excerpt: "作品集"
sitemap: true
permalink: /works/
# header:
#   overlay_image: /assets/imgs/Taipei_Night.jpg
#   caption: "台北夜景"
author_profile: true
toc: true
---
內容目前正在施工中
{: .notice--danger}

<section class="archive-post-list">

   {% for post in site.posts %}
       {% assign currentDate = post.date | date: "%Y" %}
       {% if currentDate != myDate %}
           {% unless forloop.first %}</ul>{% endunless %}
           <h1>{{ currentDate }}</h1>
           <ul>
           {% assign myDate = currentDate %}
       {% endif %}
       <li><a href="{{ post.url }}"><span>{{ post.date | date: "%B %-d, %Y" }}</span> - {{ post.title }}</a></li>
       {% if forloop.last %}</ul>{% endif %}
   {% endfor %}

</section>