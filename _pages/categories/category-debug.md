---
title: "NO 삽질"
layout: archive
permalink: categories/debug
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.debug %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
