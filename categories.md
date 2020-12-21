---
layout: page
title: Categories
---

<div class="posts">
  {% assign sorted_cats = site.categories | sort %}
  {% for category in sorted_cats %}
  <h2><a class="category-title" href="{{ site.baseurl }}/categories/{{ category[0] }}">{{ category[0] }}</a></h2>
     <ul>
       {% for post in category[1] %}
         <li><a href="{{ post.url }}">{{ post.title }}</a></li>
       {% endfor %}
     </ul>
  {% endfor %}
</div>
