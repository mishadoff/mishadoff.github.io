---
layout: page
header_title: mishadoff thoughts
---

<div>
<ul id="nobullets" class="posts">
  {% for post in site.posts limit:10 %}
      <li>
        <h1>{{ post.title }}</h1>
	  <p>
	    {{ post.excerpt }}
	  </p>
	  <a href="{{ post.url | prepend: site.baseurl }}">Read on &rarr;</a>
	  </li>
	  <div id="empty"></div>
  {% endfor %}
</ul>
</div>
