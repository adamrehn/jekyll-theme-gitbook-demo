---
title: Admin
pagenum: 2
---

[Admin](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#module-django.contrib.admin){:target="_blank"} allows us to make our models viewable in admin panel that Django automatically provides us, manipulate and control over data that is stored in database. In this post, we will go over how I designed the admin for each models.<br><br>

Generally, there are two ways we can include our models in `admin.py`. First one is making [admin object](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#modeladmin-objects){:target="_blank"}. Or, you can use [register decorator](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#the-register-decorator){:target="_blank"}. In this case, I will use [register decorator](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#the-register-decorator){:target="_blank"} one.

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

## *products/*

The following code is admin for *products/*.<br>
```python
@admin.register(models.Product)
class ProductAdmin(admin.ModelAdmin):

    ''' ProductAdmin Definition ''' 

    list_display = (
        'product_name', 
        'product_price', 
        'product_color', 
        'product_size', 
        'product_category', 
        'is_onsale', 
        'is_newarrival', 
        'is_preorder',
    )

    list_filter = (
        'is_onsale',
        'is_newarrival',
        'is_preorder',
    )

    ordering = ('product_name', 'product_price', 'product_category')

    search_fields = [
        'product_name',
        'product_category'
    ]

    fieldsets = (
        (None, {
            'fields': ('product_name', 'product_price', 'product_color', 'product_size', 'product_category')
        }),
        ('Advanced Options', {
            'classes': ('collapse',),
            'fields': ('is_onsale', 'is_newarrival', 'is_preorder', 'product_description'),
        }),
    )
```
<br>

Everything is as same as `users/` admin, but I included couple of more [admin options](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#modeladmin-options){:target="_blank"}.

1. [`search_fields`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.search_fields){:target="_blank"}: This option allows us to create the search bar on top of the admin panel so that we can search certain items that fall into our conditions.<br>
In this case, I only chose `product_name` and `product_category` as those two fields are unique fields to search the products. 

2. [`ordering`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.ordering){:target="_blank"}: This option generates the ordering feature of django admin objects.<br>
I chose `product_name`, `product_price` and `product_category` for ordering.

3. [`fieldsets`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.fieldsets){:target="_blank"}: This option provides us with customized layouts that we want.<br>
I put all fields that are directly related to `products` into normal fields as I think they are most important basic information of products. Rest of fields are sorted into `Advanced Options` with collapse fieldsets. 


<br>

## *orders/*

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
