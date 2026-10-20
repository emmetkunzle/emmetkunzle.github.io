---
layout: page
---

<section class="portfolio-grid">
  <h1>My Projects</h1>
  <ul>
    {% for post in site.categories.project %}
      <li class="project-card">
        <h3>
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </h3>
        <p>{{ post.excerpt | strip_html }}</p>
        {% if post.image %}
            <img src="{{ post.image | relative_url }}" alt="{{ post.title }}" width = "400">
        {% endif %}
        <a href="{{ post.url | relative_url }}" class="read-more">View Project &rarr;</a>
      </li>
    {% endfor %}
  </ul>
</section>

