---
layout: page
title: "Archive"
---
<img src="/images/common/archive.jpg" alt="Archive image"/>

<section id="archive">
  <ul>
      {% for post in site.posts %}
      <li>
        <b>{{ post.date | date: '%-d %B %Y' }}</b>
        <br>
        <a href="{{ post.url }}">{{post.title}}</a>
      </li>
      {% endfor %}
  </ul>
</section>
