---
# the default layout is 'page'
Title: Gone Outside
permalink: /outside/
icon: fas fa-gamepad
css: "/assets/css/outside.css"
order: 4
---

Go Outside!

<div class="row">
  {% for item in site.data.watchlist %}
    <div class="column">
      <div class="content">
        <img src="{{ item.image }}" alt="{{ item.alt }}" style="width:100%">
        <h3>{{ item.title }}</h3>
        <p>{{ item.description }}</p>
      </div>
    </div>
  {% endfor %}
</div>