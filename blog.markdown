---
title: Blog
date: 2021-01-14 13:55:00 +01:00
position: 2
site-title: HeyCSV Blog - How to deal with data files?
description: Making .csv file handling easy! The latest tips and tricks on how to
  manage .csv data files more effectively.
---

<ul class="post-list">
	{% assign sorted = site.blog | reverse %}
    {% for post in sorted %}
      <li>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>

        <h2>
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
        </h2>
      </li>
    {% endfor %}
 </ul>