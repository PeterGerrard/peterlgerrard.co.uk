---
layout: default
---

Here are the things I am currently looking at:

{% for investigating_hash in site.data.learningbacklog.investigating %}
{% assign investigating = investigating_hash[1] %}
## {{ investigating.title }}
{{ investigating.details }}
### Resources
{% for resource in investigating.resources %}
- {{ resource }}
{% endfor %}
{% endfor %}