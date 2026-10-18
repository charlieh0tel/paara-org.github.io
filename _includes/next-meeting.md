{%- include find-meetings.html %}
## {% if next_is_upcoming %}Next club meeting{% else %}Most recent club meeting{% endif %}
{% include meeting-facts.md meeting=next_meeting %}{% if next_is_upcoming %}{% include zoom-details.md %}{% endif %}

For details, see the [{{ next_meeting.title }}]({{ next_meeting.url | relative_url }}) page.
