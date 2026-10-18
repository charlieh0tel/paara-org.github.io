{%- comment -%}
  Date/time/topic/presenter bullets for the meeting passed as include.meeting.
{%- endcomment -%}
{%- assign m = include.meeting -%}
{%- if m.date -%}
  {%- assign when = m.date | date: "%B %-d, %Y" -%}
{%- else -%}
  {%- assign ym = m.path | split: "/" | last | slice: 0, 6 -%}
  {%- capture first_day %}{{ ym | slice: 0, 4 }}-{{ ym | slice: 4, 2 }}-01{% endcapture -%}
  {%- assign when = first_day | date: "%B %Y" -%}
{%- endif -%}
{%- assign presenters = m.presenter | join: "|||" | split: "|||" %}
* **Date**: `{{ when }}`
* **Time**: `{{ m.time | default: "07:00 PM Pacific Time" }}`
{% if m.talks -%}
{% for talk in m.talks -%}
* **Topic #{{ forloop.index }}**: `{{ talk.topic }}` by `{{ talk.presenter }}`
{% endfor -%}
{% else -%}
* **Topic**: `{{ m.topic }}`
{% for detail in m.topic_details -%}
   * {{ detail }}
{% endfor -%}
{% if presenters.size > 1 -%}
* **Presenter**:
{% for p in presenters -%}
   * `{{ p }}`
{% endfor -%}
{% elsif presenters.size == 1 -%}
* **Presenter**: {% if m.presenter_url %}[`{{ presenters.first }}`]({{ m.presenter_url }}){% else %}`{{ presenters.first }}`{% endif %}
{% endif -%}
{% endif -%}
