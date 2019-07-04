---
layout: default
---

{% for post in site.posts %}
    {{ post.date | date_to_string }} [{{ post.title }}]({{ site.baseurl }}{{ post.url }})
{% endfor %}
