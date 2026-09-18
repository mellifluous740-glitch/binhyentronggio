---
layout: default
title: Thư viện truyện
permalink: /truyen/
---
<header class="page-heading"><p class="eyebrow">Thư viện</p><h1>Truyện</h1><p>Chọn một câu chuyện và bắt đầu đọc.</p></header><div class="grid">{% assign stories = site.stories | sort: 'updated_at' | reverse %}{% for story in stories %}<article class="card">{% if story.cover_image %}<img src="{{ story.cover_image }}" alt="Bìa truyện {{ story.title | escape }}">{% endif %}<div class="card-content"><p class="eyebrow">{{ story.status | replace: 'ongoing', 'Đang ra' | replace: 'completed', 'Hoàn thành' }}</p><h2><a href="{{ story.url | relative_url }}">{{ story.title }}</a></h2><p>{{ story.summary | truncate: 150 }}</p><p>{% for genre in story.genres %}<span class="tag">{{ genre }}</span>{% endfor %}</p></div></article>{% else %}<p>Chưa có truyện nào.</p>{% endfor %}</div>
