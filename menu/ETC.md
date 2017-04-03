---
layout: page
title: ETC
---

<ul class="posts">
  {% for post in site.posts %}
    {% for etcPost in post.tags.ETC %}

      {% unless etcPost.next %}
        <h3>{{ etcPost.date | date: '%Y' }}</h3>
        {% else %}
        {% capture year %}{{ etcPost.date | etcPost: '%Y' }}{% endcapture %}
        {% capture nyear %}{{ etcPost.next.date | date: '%Y' }}{% endcapture %}
        {% if year != nyear %}
          <h3>{{ etcPost.date | date: '%Y' }}</h3>
        {% endif %}
      {% endunless %}

      <li itemscope>
        <a href="{{ site.github.url }}{{ etcPost.url }}">{{ etcPost.title }}</a>
        <p class="post-date"><span><i class="fa fa-calendar" aria-hidden="true"></i> {{ etcPost.date | date: "%B %-d" }} - <i class="fa fa-clock-o" aria-hidden="true"></i> {% include read-time.html %}</span></p>
      </li>
      {% endfor %}
  {% endfor %}
</ul>
