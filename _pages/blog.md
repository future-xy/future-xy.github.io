---
layout: page
title: blog
permalink: /blog/
description: Thoughts on LLM inference, systems research, and machine learning.
nav: true
nav_order: 3
pagination:
  enabled: true
  collection: posts
  permalink: /page/:num/
  per_page: 5
  sort_field: date
  sort_reverse: true
  trail:
    before: 1
    after: 3
---

{%- if site.posts.size > 0 -%}
  <ul class="post-list">
    {%- assign featured_posts = site.posts | where: "featured", "true" -%}
    {% if featured_posts.size > 0 %}
      <br>
      <h3><strong><i class="fa-solid fa-fire"></i> Featured</strong></h3>
      {% for post in featured_posts %}
        <li>
          <h3>
            {% if post.redirect == blank %}
              <a class="post-title" href="{{ post.url | relative_url }}">{{ post.title }}</a>
            {% elsif post.redirect contains '://' %}
              <a class="post-title" href="{{ post.redirect }}" target="_blank">{{ post.title }}</a>
            {% else %}
              <a class="post-title" href="{{ post.redirect | relative_url }}">{{ post.title }}</a>
            {% endif %}
          </h3>
          <p>{{ post.description }}</p>
          <p class="post-meta">
            {{ post.date | date: '%B %d, %Y' }}
          </p>
          <p class="post-tags">
            <a href="{{ post.date | date: '%Y' | prepend: '/blog/' | prepend: site.baseurl }}">
              <i class="fa-solid fa-calendar fa-sm"></i> {{ post.date | date: '%Y' }}
            </a>
            &nbsp; &middot; &nbsp;
            {% for tag in post.tags %}
              <a href="{{ tag | slugify | prepend: '/blog/tag/' | prepend: site.baseurl }}">
                <i class="fa-solid fa-hashtag fa-sm"></i> {{ tag }}</a>&nbsp;
            {% endfor %}
            {% for category in post.categories %}
              &nbsp; &middot; &nbsp;
              <a href="{{ category | slugify | prepend: '/blog/category/' | prepend: site.baseurl }}">
                <i class="fa-solid fa-tag fa-sm"></i> {{ category }}</a>&nbsp;
            {% endfor %}
          </p>
        </li>
      {% endfor %}
      <hr>
    {% endif %}

    {%- assign paginated_posts = paginator.posts -%}
    {% for post in paginated_posts %}
      <li>
        <h3>
          {% if post.redirect == blank %}
            <a class="post-title" href="{{ post.url | relative_url }}">{{ post.title }}</a>
          {% elsif post.redirect contains '://' %}
            <a class="post-title" href="{{ post.redirect }}" target="_blank">{{ post.title }}</a>
          {% else %}
            <a class="post-title" href="{{ post.redirect | relative_url }}">{{ post.title }}</a>
          {% endif %}
        </h3>
        <p>{{ post.description }}</p>
        <p class="post-meta">
          {{ post.date | date: '%B %d, %Y' }}
        </p>
        <p class="post-tags">
          <a href="{{ post.date | date: '%Y' | prepend: '/blog/' | prepend: site.baseurl }}">
            <i class="fa-solid fa-calendar fa-sm"></i> {{ post.date | date: '%Y' }}
          </a>
          &nbsp; &middot; &nbsp;
          {% for tag in post.tags %}
            <a href="{{ tag | slugify | prepend: '/blog/tag/' | prepend: site.baseurl }}">
              <i class="fa-solid fa-hashtag fa-sm"></i> {{ tag }}</a>&nbsp;
          {% endfor %}
          {% for category in post.categories %}
            &nbsp; &middot; &nbsp;
            <a href="{{ category | slugify | prepend: '/blog/category/' | prepend: site.baseurl }}">
              <i class="fa-solid fa-tag fa-sm"></i> {{ category }}</a>&nbsp;
          {% endfor %}
        </p>
      </li>
    {% endfor %}
  </ul>

  {% include pagination.liquid %}
{%- else -%}
  <p>No posts so far...</p>
{%- endif -%}
