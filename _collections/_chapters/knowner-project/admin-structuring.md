---
title: Admin
pagenum: 2
---

[Admin](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#module-django.contrib.admin){:target="_blank"} allows us to make our models viewable in admin panel that Django automatically provides us, manipulate and control over data that is stored in database. In this post, we will go over how I designed the admin for each models.<br><br>

Generally, there are two ways we can include our models in `admin.py`. First one is making [admin object](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#modeladmin-objects){:target="_blank"}. Or, you can use [`register` decorator](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#the-register-decorator){:target="_blank"}. In this case, I will use [`register` decorator](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#the-register-decorator){:target="_blank"} one.

## *users/*

The following code is admin for *users/*.<br>
```python
from django.contrib import admin
from .models import User # Importing User model from models.py

@admin.register(User)
class UserAdmin(admin.ModelAdmin):

    ''' User Admin Definition '''

    list_display = ('username', 'email', 'language', 'currency')
    list_filter = ('language', 'currency')
```
<br>

I made a class for user admin as `UserAdmin` and inherited from `admin.ModelAdmin`.<br><br>

Inside the admin model, we can add [options](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#modeladmin-options){:target="_blank"} for customizing the admin class object.
1. [`list_display`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display){:target="_blank"}: This option allows us to literally display all fields as the admin panel table's field names. You can use either tuple or array. In this case, I included `username`, `email`, `language` and `currency`. 
2. [`list_filter`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_filter){:target="_blank"}: This option allows us to generate filter feature. I included Boolean type model fields only, which are `language` and `currency`.

<br>





[^1]: [Markdown Options - Jekyll Documentation](https://jekyllrb.com/docs/configuration/markdown/)
[^2]: [Kramdown homepage](https://kramdown.gettalong.org/)
