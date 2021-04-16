---

title: SH4D3S CryptoQuery

---
# {{ page.title }}

## Instructions
* Do this and then
* Do that and then
* Answer answer answer


## Questions

{% for s in site.data.cryptoqueries %}
###{{ s.section }}
{% for q in s.questions %}
**Q: {{ q.q }}**
{% if q.type == 'singlechoice' %}
{% for a in q.answers %}
<input type='radio' name='{{s.section}}' value='{{a.a}}'/>
{% endfor %}
{% endif %}
{% endfor %}
{% endfor %}
