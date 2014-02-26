---
layout: default
---
<div class="index-content">
	<div class="section">
        <ul class="artical-list">
            {% for post in site.categories.Javascript %}
            <li>
                <h2>
                    <a href="{{ post.url }}">{{ post.title }}</a>
                </h2>
                <div "title-desc">{{ post.description }}</div>
            </li>
            {% endfor %}
            
            {% for post in site.categories.JAVA %}
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