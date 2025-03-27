---
layout: home
---

## 最新文章

{% for post in site.posts %}
  <article class="post-card">
    <h2>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </h2>
    <p class="post-meta">{{ post.date | date: "%Y-%m-%d" }}</p>
    <p class="post-excerpt">{{ post.excerpt }}</p>
  </article>
{% endfor %}
