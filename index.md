---
layout: default
---
<div class="index-content">
	<div class="section">
	javascript {{ site.categories.javascript.size }}
        <ul class="artical-list">
            {% for post in site.categories.javascript %}
            <li>
                <h2>
                    <a href="{{ post.url }}">{{ post.title }}</a>
                </h2>
                <div "title-desc">{{ post.description }}</div>
            </li>
            {% endfor %}
            
            {% for post in site.categories.java %}
            <li>
                <h2>
                    <a href="{{ post.url }}">{{ post.title }}</a>
                </h2>
                <div "title-desc">{{ post.description }}</div>
            </li>
            {% endfor %}
        </ul>
    </div>
</div>
