---
layout: page
title: My Block
permalink: /block/
---

<div class="main">
	<section id="pm_blog" class="row">
	  <ul class="post-list">
	    {% for post in site.posts %}
	      <li>
	        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
	        <h2>
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