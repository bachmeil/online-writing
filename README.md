## Posts

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>

<hr style="width: 65%;">

## About

You can find out more about me [on my website](https://lancebachmeier.com/).