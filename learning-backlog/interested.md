---
layout: default
---
Here are a list of things I might look at, and possible resources to use:

# Next
{% for next_hash in site.data.learningbacklog.interested.next %}
{% assign next = next_hash[1] %}
## {{ next.title }}
{{ next.details }}
### Resources
{% for resource in next.resources %}
- {{ resource }}
{% endfor %}

[Permalink]({{ next.title | datapage_url: "item" }})
{% endfor %}

# Possible
{% for possible_hash in site.data.learningbacklog.interested.possible %}
{% assign possible = possible_hash[1] %}
## {{ possible.title }}
{{ possible.details }}
### Resources
{% for resource in possible.resources %}
- {{ resource }}
{% endfor %}

[Permalink]({{ possible.title | datapage_url: "item" }})
{% endfor %}

# Unranked
{% for unranked_hash in site.data.learningbacklog.interested.unranked %}
{% assign unranked = unranked_hash[1] %}
## {{ unranked.title }}
{{ unranked.details }}
### Resources
{% for resource in unranked.resources %}
- {{ resource }}
{% endfor %}

[Permalink]({{ unranked.title | datapage_url: "item" }})
{% endfor %}
