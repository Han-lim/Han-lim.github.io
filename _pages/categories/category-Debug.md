---
title: "NO 삽질"
layout: archive
permalink: categories/Debug
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.Debug %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
