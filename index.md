---
layout: page
---


<br>

<section class="portfolio-grid">
  <h2>My Portfolio Projects</h2>
  <ul>
    {% for post in site.categories.project %}
      <li class="project-card">
        <h3>
          <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
        </h3>
        <p>{{ post.excerpt | strip_html }}</p>
        <a href="{{ post.url | relative_url }}" class="read-more">View Project &rarr;</a>
      </li>
    {% endfor %}
  </ul>
</section>

