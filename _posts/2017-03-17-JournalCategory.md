---
layout: post
title: "Journal"
categories: Test
tags: [category]
image:
  feature: spools.jpg
  teaser: spools-teaser.jpg
  credit:
  creditlink:
---

<ul class ="posts">
  {% for post in site.posts %}
    {% if post.category == "journal" %}
      <li itemscope>
      <a href="{{ site.github.url }}{{ post.url }}">{{ post.title }}</a>
      <p class="post-date"><span><i class="fa fa-calendar" aria-hidden="true"></i> {{ post.date | date: "%B %-d" }} - <i class="fa fa-clock-o" aria-hidden="true"></i> {% include read-time.html %}</span></p>
      </li>
    <% endif %>  
  {% endfor %}
</ul>


<ul class="posts">
  {% for post in site.posts %}

    {% if post.category == "journal" %}
      <li itemscope>
      <a href="{{ site.github.url }}{{ post.url }}">{{ post.title }}</a>
      <p class="post-date"><span><i class="fa fa-calendar" aria-hidden="true"></i> {{ post.date | date: "%B %-d" }} - <i class="fa fa-clock-o" aria-hidden="true"></i> {% include read-time.html %}</span></p>
    </li>
    {% endif %}  

  {% endfor %}
</ul>
