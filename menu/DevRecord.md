---
layout: page
title: Dev Record
---

<ul class="posts">
  {% for post in site.tags.Record %}

      {% unless post.next %}
        <h3>{{ post.date | date: '%Y' }}</h3>
        {% else %}
        {% capture year %}{{ post.date | post: '%Y' }}{% endcapture %}
        {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
        {% if year != nyear %}
          <h3>{{ post.date | date: '%Y' }}</h3>
        {% endif %}
      {% endunless %}

      <li itemscope>
        <a href="{{ site.github.url }}{{ post.url }}">{{ post.title }}</a>
        <p class="post-date"><span><i class="fa fa-calendar" aria-hidden="true"></i> {{ post.date | date: "%B %-d" }} - <i class="fa fa-clock-o" aria-hidden="true"></i> {% include read-time.html %}</span></p>
      </li>
  {% endfor %}
</ul>
