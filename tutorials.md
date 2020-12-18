---
layout: default
title: Tutorials
---

<div class="posts">
{% for category in site.categories | where: 'category', 'tutorials' %}
    {% for post in category[1] %}
          <article class="post" role="article">
      <h2><a href="{{ site.baseurl }}{{ post.url }}">
        {{ post.title }}
      </a></h2>
      <p>{{ post.summary }}</p>
      <span class="post-date">
        <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B" }}</time>
        <time class="date-day" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%d" }},</time>
        <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y" }}</time>
      </span>
    </article>
    {% endfor %}
{% endfor %}
</div>
