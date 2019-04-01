---
title: Test looping
tag: somethin
order: 10
---

<h1> This is a test to demostrate including dynamic data </h1>

Drawing from site.data.projects directly, we have these projects available:

{% for item in site.data.projects %}
* {{ item.name }}
{% endfor %}

<hr>
Advisors for this project.  This first example uses a separate yaml file in \_data/advisors/PlasmoDB.yml.  It is likely more convenient for end users to use the projects data structure in \_/data/advisors/projects.yml directly to keep the data in a single file.  I've left it here for demonstration purposes.

{% for a in site.data.advisors.PlasmoDB %}
* <a href="{{ a.homepage }}">{{ a.name }}</a> ({{ a.institution }})
{% endfor %}

<hr>
This is the project determined by splitting the page.path.  This is needed because these sub pages are loaded by the \_layouts/help.html layout, so they do not have access to the page data structure directly. i.e. they are rendered first, before being included.  This is inconvenient, but you can determine the project from the path, instead of using the page.name accessible in the layout:

{% assign split_path = page.path | split: "/" %}
{% assign project = split_path[1] %}
{{ project }}

<hr>
We can easily pull from the separate data structure in \_data/advisors/PlasmoDB.yml using the project var obtained from the path above.

{{ site.data.advisors[project] }}

<hr>

Here we reference the advisors inside the projects data structure (in \_data/projects.yml) by using assignments.  This is tricky, because we can't reference the nested structures directly (at least not that I know of)  So we filter all the project data by the project, which gives us a list. Since we only want one item in the list, assuming project names are unique, we get the first item.  We can then reference the advisors in that item.

{% assign project_data_list = site.data.projects | where: "name", project %}
{% assign project_data = project_data_list[0] %}


{% for a in project_data['advisors']  %}
* <a href="{{ a.homepage }}">{{ a.name }}</a> ({{ a.institution }})
{% endfor %}

<hr>

Here is another, probably easier and more straightforward, way to get data from the projects structure which avoids the weird assignments of the last example.

{% for item in site.data.projects %}
  {% if item.name == project %}
    {% for advisor in item.advisors %}
* <a href="{{ advisor.homepage }}">{{ advisor.name }}</a> ({{ advisor.institution }})
    {% endfor %}
  {% endif %}
{% endfor %}

<hr>

You can use this same method to reference the data structure directly, without filtering by project.  This is the same method as above, just without the if block :

{% for item in site.data.projects %}
<h3> {{ item.name }} </h3>
  Advisors:
  {% for advisor in item.advisors %}
    * {{ advisor.name }}
  {% endfor %}

{% endfor %}

