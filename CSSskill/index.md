---
layout: default
---

{% for post in site.categories.CSSskill %}
            <li>
                <h2>
                    <a href="{{ post.url }}">{{ post.title }}</a>
                </h2>
                <div "title-desc">{{ post.description }}</div>
            </li>
{% endfor %}