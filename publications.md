---
layout: page
title: Publications
permalink: /publications/
---
Below is a chronologically ordered list of my published pieces.

<ul>
{% for pub in site.data.publications %}
  <li>
    <strong><em>{{ pub.publisher }}</em></strong>: <a href="{{ pub.link }}" target="_blank">{{ pub.title }}</a>
    <br>
    <em>{{ pub.date | date_to_long_string }}</em>
    <p/>
    <blockquote>{{ pub.excerpt }}</blockquote>
  </li>
{% endfor %}
</ul>
