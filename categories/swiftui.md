---
layout: default
title: SwiftUI
tags: category
---

<div class="category-posts">
    {% for post in site.posts %}
        {% if post.category == "SwiftUI" %}
            <article class="post" role="article">
              <h2><a href="{{ site.baseurl }}{{ post.url }}">
                {{ post.title }}
              </a></h2>
                    <span class="post-date">
                <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B" }}</time>
                <time class="date-day" datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%d" }},</time>
                <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y" }}</time>
              </span>
              <p>{{ post.summary }}</p>
            </article>
        {% endif %}
  {% endfor %}
    
</div>
