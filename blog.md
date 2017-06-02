---
layout: page
title: My Blog
permalink: /block/
---

<div class="container">
	<section id="pm_blog">
	  <ul class="post-list">
	    {% for post in site.posts %}
	      <li>
	        <!--<span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>-->
	        <h2 style="margin-top:0px;">
	          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
	        </h2>
	        <div class="pm_justify pm_text_normal">
	        	{{ post.content }}
	        </div>
	      </li>
	    {% endfor %}
	  </ul>
	</section>
</div>
