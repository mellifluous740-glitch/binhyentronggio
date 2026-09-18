---
layout: default
title: Trang chủ
---
<section class="hero"><p class="eyebrow">Mellifluous</p><h1>Bình yên trong gió</h1><p>Một góc nhỏ để chậm rãi đọc những câu chuyện dịu dàng.</p><a class="button" href="{{ '/truyen/' | relative_url }}">Khám phá truyện</a></section>
<section><div class="section-heading"><h2>Truyện mới</h2><a href="{{ '/truyen/' | relative_url }}">Xem tất cả →</a></div><div class="grid">{% assign recent_stories = site.stories | sort: 'updated_at' | reverse %}{% for story in recent_stories limit: 6 %}<article class="card">{% if story.cover_image %}<img src="{{ story.cover_image }}" alt="Bìa truyện {{ story.title | escape }}">{% endif %}<div class="card-content"><p class="eyebrow">{{ story.status | replace: 'ongoing', 'Đang ra' | replace: 'completed', 'Hoàn thành' }}</p><h3><a href="{{ story.url | relative_url }}">{{ story.title }}</a></h3><p>{{ story.summary | truncate: 130 }}</p></div></article>{% else %}<p>Chưa có truyện nào.</p>{% endfor %}</div></section>
<section id="thong-bao"><h2>Thông báo</h2>{% for notice in site.data.announcements %}<article class="notice"><p class="eyebrow">{{ notice.tag }} · {{ notice.date }}</p><h3>{{ notice.title }}</h3><p>{{ notice.content }}</p></article>{% endfor %}</section>
