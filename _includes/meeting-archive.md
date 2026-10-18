{%- comment -%}
  Past meetings, grouped by year, newest first. Meetings dated today or
  later are left out until they have happened.
{%- endcomment -%}
{%- include find-meetings.html -%}
{%- assign year = "" -%}
{%- for m in meetings -%}
  {%- if m.date -%}
    {%- assign d = m.date | date: "%Y%m%d" -%}
    {%- if d >= today %}{% continue %}{% endif -%}
  {%- endif -%}
  {%- assign ym = m.path | split: "/" | last | slice: 0, 6 -%}
  {%- assign y = ym | slice: 0, 4 -%}
  {%- if m.talks -%}
    {%- assign who = m.talks | map: "presenter" | join: "; " -%}
    {%- assign what = m.talks | map: "topic" | join: "; " -%}
  {%- else -%}
    {%- assign who = m.presenter | join: "; " -%}
    {%- assign what = m.topic -%}
  {%- endif -%}
  {%- if y != year %}

### {{ y }}

{% assign year = y -%}
  {%- endif %}
* [{{ ym }}]({{ m.url | relative_url }}) **{{ who }}**: `{{ what }}`{% if m.content contains "youtube.com/embed" %} (<mark>with video</mark>){% endif %}
{%- endfor %}
