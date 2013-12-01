---
layout: default
---
    <ul >
        {% for post in site.categories.project %}
            <li>
                <h2>
                    <a href="{{ post.url }}">{{ post.title }}</a>
                </h2>
                <div >{{ post.description }}</div>
            </li>
        {% endfor %}
    </ul>