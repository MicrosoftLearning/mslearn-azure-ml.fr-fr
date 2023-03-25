---
title: Instructions hébergées en ligne
permalink: index.html
layout: home
---

# Microsoft Learn - Exercices pratiques

Les exercices pratiques suivants sont conçus pour prendre en charge les formations [Microsoft Learn](https://docs.microsoft.com/training/).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %}
| |
| --- | --- | 
{% for activity in labs  %}| [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
