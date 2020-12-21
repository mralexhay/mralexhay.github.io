---
layout: default
title: Categories
---

<div class="posts">
  <h1>Categories</h1>
  {% assign sorted_cats = site.categories | sort %}
  {% for category in sorted_cats %}
     <h3 class="category-title">{{ category[0] }}</h3>
     <ul>
       {% for post in category[1] %}
         <li><a href="{{ post.url }}">{{ post.title }}</a></li>
       {% endfor %}
     </ul>
  {% endfor %}
</div>
