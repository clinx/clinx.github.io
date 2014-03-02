---
layout: default
---

<div class="index-content clearfix">    
    <div class="section subhome">
        <div class="artical-content artical-list">
            <ul id="javalist">
                {% for post in site.categories.java %}
                    <li class="post">
                        <a href="{{ post.url }}">{{ post.title }}</a>
                        <div class="title-desc">{{ post.description }}</div>
                    </li>
                {% endfor %}
            </ul>
        </div>
    </div>
    <div class="navbar">
        <div class="logo"><span>UP down UP</span></div>
        <ul>
            <li>
                <a href="/">HOME</a>
            </li>
            <li>
                 <a href="/JAVASCRIPT">JAVASCRIPT({{ site.categories.javascript.size }})</a>
            </li>
            <li>
                 <a href="/CSSskill">CSS</a>                
            </li>
            <li>
                 <a href="/JAVA">JAVA({{ site.categories.java.size }})</a> 
            </li>
            <li>
                 <a href="/MATH">MATH</a> 
            </li>
        </ul>
    </div>
    <div class="splitline">
    </div>
</div>