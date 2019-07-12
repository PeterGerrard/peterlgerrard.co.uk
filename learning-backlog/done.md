---
layout: default
---

Here are things I have finished looking at, along with resouces I used.

{% for done_hash in site.data.learningbacklog.done %}
{% assign done = done_hash[1] %}
## {{ done.title }}
{{ done.details }}
### Resources
{% for resource in done.resources %}
- {{ resource }}
{% endfor %}

[Permalink]({{ done.title | datapage_url: "item" }})
{% endfor %}