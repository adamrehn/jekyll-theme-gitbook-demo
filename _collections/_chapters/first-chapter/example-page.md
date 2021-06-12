---
title: Structuring models 
pagenum: 1
---

[Models](https://docs.djangoproject.com/en/3.2/topics/db/models/#module-django.db.models) is the way of defining how our data looks like. On the web app, any data that changes will be defined in the models. 


## `users/` 

For User model, I decided to use [`AbstractUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project) so that I can have customized models with built-in fields.

#### Here's a nested subheading

Text for this nested subheading goes here. Note that the nested subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.

### Here's another subheading

Text for this subheading goes here. Note that the subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.

#### Here's another nested subheading

Text for this nested subheading goes here. Note that the nested subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.

#### Here's yet another nested subheading

Text for this nested subheading goes here. Note that the nested subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.





## Alerts

These custom alerts ship with the theme, but you can also use the [alert classes provided by Bootstrap](https://getbootstrap.com/docs/4.5/components/alerts/) if you prefer those instead.

{% include alerts/info.html content="This is an informational alert." %}

{% include alerts/warning.html content="This is a warning alert." %}


## Code fences

The theme formats the boxes for code fences, but does not provide CSS styles for syntax highlighting, since Jekyll sites can use a [number of different markdown processing engines](https://jekyllrb.com/docs/configuration/markdown/) (including custom processing plugins) and guaranteeing compatibility with the syntax highlighting output of all of them is not feasible.

{% highlight bash %}
# This is a comment, and will be styled as such if you add CSS styles for syntax highlighting
echo "This is a command with a string, and will also be styled when appropriate CSS styles are used"
{% endhighlight %}


## Citations and references

This block of text contains a number of inline citations, such as this one[^1] and this one[^2]. You can also repeat citations if multiple sentences refer to the same source[^1]. The citation/reference linking functionality is actually provided by the markdown processor that Jekyll is using to render the page ([Kramdown](https://kramdown.gettalong.org/) in the case of this demo site), but the theme does provide CSS style rules to make the inline citations look nice.

Here is the reference list that the inline citations link to:

[^1]: [Markdown Options - Jekyll Documentation](https://jekyllrb.com/docs/configuration/markdown/)
[^2]: [Kramdown homepage](https://kramdown.gettalong.org/)
