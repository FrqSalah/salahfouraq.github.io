---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

{%- for post in site.posts limit: 1 -%}
  <h2>{{ post.title }}</h2>
  <img src="{{ post.image }}" alt="{{ post.title }}">
  <p>{{ post.excerpt }}</p>
{%- endfor -%}