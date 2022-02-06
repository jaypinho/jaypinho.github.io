---
layout: page
title: Public Events & Interviews
permalink: /public-speaking/
---
Below is a chronologically ordered list of my public speaking events, webinars, panels, and interviews.

{% for event in site.data.public-speaking %}
<h3>{{ event.title }}</h3>
<small><em>{{ event.host }}, {{ event.date | date_to_long_string }}</em></small>
<blockquote>{{ event.excerpt }}</blockquote>
{% for link in event.links %}
  {% if link.type == 'embed' %}
  {{ link.url }}
  <br>
  {% else %}
  <a href="{{ link.url }}" target="_blank">{{ link.title }} 🔗</a>
  <br>
  {% endif %}
{% endfor %}
<br>
<p/>
{% endfor %}