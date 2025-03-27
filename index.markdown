---
layout: home
title: Home
---

<div class="hero-section">
  <h1>Welcome to Willson's Tech World</h1>
  <p class="subtitle">探索技术，分享知识，记录成长</p>
</div>

## 最新文章

<div class="featured-posts">
  {% for post in site.posts limit:3 %}
    <div class="post-card">
      <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
      <span class="post-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
      <p>{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
    </div>
  {% endfor %}
</div>

## 进一步

- 🌐 Web 开发
- 📱 移动应用
- 🛠️ 开发工具
- 🤖 人工智能

## 最近项目

<div class="project-grid">
  {% for project in site.data.projects limit:2 %}
    <div class="project-card">
      <h3>{{ project.name }}</h3>
      <p>{{ project.description }}</p>
      <a href="{{ project.url }}" class="project-link">了解更多</a>
    </div>
  {% endfor %}
</div>
