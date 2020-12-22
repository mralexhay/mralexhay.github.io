---
layout: default
title: Toolbox Pro
tags: category
---

<div class="category-posts">
    {% for post in site.posts %}
        {% if post.category == "Toolbox Pro" %}
            <article class="post" role="article">
              <h2><a class="post-link" href="{{ site.baseurl }}{{ post.url }}">
                {{ post.title }}
              </a></h2>
                    <span class="post-date">
                <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B" }}</time>
                <time class="date-day" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%d" }},</time>
                <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y" }}</time>
              </span>
              <p class="post-summary">{{ post.summary }}</p>
            </article>
        {% endif %}
  {% endfor %}
    
</div>

