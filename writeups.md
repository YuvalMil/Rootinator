---
layout: page
title: Writeups
permalink: /writeups/
---

## CTF Writeups & Bug Bounty Reports

<ul>
  {% for writeup in site.writeups %}
    <li>
      <a href="{{ writeup.url }}">{{ writeup.title }}</a> - {{ writeup.date | date: "%B %d, %Y" }}
    </li>
  {% endfor %}
</ul>

<h3>Blog Posts</h3>
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a> - {{ post.date | date: "%B %d, %Y" }}
    </li>
  {% endfor %}
</ul>