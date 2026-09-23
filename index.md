# Welcome to Digging Deeper

Diving into Enterprise Virtualization, Hypervisor Architecture, and Cloud Infrastructure

## Recent Posts

{% for post in site.posts %}
* **[{{ post.title }}]({{ post.url | relative_url }})** - *{{ post.date | date: "%B %d, %Y" }}*
  {{ post.excerpt }}
{% endfor %}
