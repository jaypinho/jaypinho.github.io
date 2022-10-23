---
layout: page
title: Press Mentions
permalink: /press-mentions/
---
Below is a chronologically ordered list of press mentions.

<ul>
{% for mention in site.data.press-mentions %}
  <li>
    <strong><em>{{ mention.publisher }}</em></strong>: <a href="{{ mention.link }}" target="_blank">{{ mention.title }}</a>
    <br>
    <em>{{ mention.date | date_to_long_string }}</em>
    <p/>
    <blockquote>{{ mention.excerpt }}</blockquote>
  </li>
{% endfor %}
</ul>
