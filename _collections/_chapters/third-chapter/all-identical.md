---
title: The pages are all identical
pagenum: 2
---

This is an example page demonstrating various types of content and their interactions with the features provided by the theme.


## Heading levels

### Here's a subheading

Text for this subheading goes here. Note that the subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.

#### Here's a nested subheading

Text for this nested subheading goes here. Note that the nested subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.

### Here's another subheading

Text for this subheading goes here. Note that the subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.

#### Here's another nested subheading

Text for this nested subheading goes here. Note that the nested subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.

#### Here's yet another nested subheading

Text for this nested subheading goes here. Note that the nested subheading appears in the automatically-generated table of contents, which sits as a sidebar on the right at wider resolutions and inline with the body content at narrower resolutions.


## Tables

The theme automatically wraps tables in a `<div>` wrapper that overflows automatically on mobile devices when the table is too wide, without the need to horizontally scroll the entire page.

|Column 1            |Column 2            |Column 3            |Column 4            |Column 5            |Column 6            |Column 7            |
|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|--------------------|
|Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |
|Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |
|Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |
|Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |
|Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |
|Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |
|Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |Table cell contents |


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
