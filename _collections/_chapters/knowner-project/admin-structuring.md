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

    list_display = ('username', 'email', 'language', 'currency',)
    
    list_filter = ('language', 'currency',)

    search_fields = ('username', 'email',)
```
<br>

I made a class for user admin as `UserAdmin` and inherited from `admin.ModelAdmin`.<br><br>

### Admin options 

Inside the admin model, we can add [admin options](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#modeladmin-options){:target="_blank"} for customizing the admin class object.

1. [`list_display`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display){:target="_blank"}: This option allows us to literally display all fields as the admin panel table's field names. You can use either tuple or array. In this case, I included `username`, `email`, `language` and `currency`. 

2. [`list_filter`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_filter){:target="_blank"}: This option allows us to generate filter feature. I included Boolean type model fields only, which are `language` and `currency`.

3. [`search_fields`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.search_fields){:target="_blank"}: This option allows us to create the search bar on top of the admin panel so that we can search certain items that fall into our conditions.<br>
In this case, I only chose `username` and `email` as those two fields are unique fields to search the products. 

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

I included couple of more [admin options](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#modeladmin-options){:target="_blank"} besides of `users/` ones. 

1. [`list_display`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display){:target="_blank"}: I included all fields that I made in `Product` model.

2. [`list_filter`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_filter){:target="_blank"}: As same as I did in `UserAdmin` model, I included only Boolean model fields, which are `is_onsale`, `is_newarrival`, `is_preorder`. 

3. [`ordering`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.ordering){:target="_blank"}: This option generates the ordering feature of django admin objects.<br>
I chose `product_name`, `product_price` and `product_category` for ordering.

4. [`fieldsets`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.fieldsets){:target="_blank"}: This option provides us with customized layouts that we want.<br>
I put all fields that are directly related to `products` into basic default fields as I think they are most important basic information of products. Rest of fields are sorted within collapsable fieldsets.

<br><br>

### `TabularInline` 

To show the photos of each `Product`, I decided to create new admin model but I did not want to make it as a separate one. Instead, I used [`TabularInline`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.TabularInline){:target="_blank"} so that admin model for photo could be included within `ProductAdmin` admin model.<br><br>

Above the `ProductAdmin` admin model, I included the following code as below:

```python
class PhotoInline(admin.TabularInline):

    ''' PhotoAdmin Definition ''' 

    model = models.Photo
    extra = 1
```

`PhotoInline` is the name of [`TabularInline`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.TabularInline){:target="_blank"} admin model.<br>

1. [`model`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.InlineModelAdmin.model){:target="_blank"}: Required field. I connected to `Photo` model inside `models.py`.
2. [`extra`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.InlineModelAdmin.extra){:target="_blank"}: I provided value of `1` so that it can show extra empty slot when inputting. 
<br><br>

and then, I included the `PhotoInline` inline admin model inside the `ProductAdmin` as follows:

```python
    ...

    inlines = [
        PhotoInline,
    ]

    ...
```

<br><br>

#### Including photo inside InlineAdmin 

Additionally, I wanted to include the thumbnail preview feature inside of `ProductAdmin` admin model so that admin can easily figure out which products are those.<br><br>

To do this, I decided to make [custom model methods](https://docs.djangoproject.com/en/2.2/topics/db/models/#model-methods){:target="_blank"} to provide thumbnail pictures, so that those could be used in different areas.<br><br>

I made the [custom model method](https://docs.djangoproject.com/en/2.2/topics/db/models/#model-methods){:target="_blank"} function naming as `thumbnail_preview` and included inside the `models.py` of `products/`. 

```python
def thumbnail_preview(self):
    from django.utils.safestring import mark_safe
    return mark_safe(f'<img src={self.file.url} width=40% />')
thumbnail_preview.short_description = '미리보기'
```
<br>

Firstly, I imported [`mark_safe()`](https://docs.djangoproject.com/en/2.2/ref/utils/#django.utils.safestring.mark_safe){:target="_blank"} method. [`mark_safe()`](https://docs.djangoproject.com/en/2.2/ref/utils/#django.utils.safestring.mark_safe){:target="_blank"} provides us with HTTP url of objects in "safe" way so that Django won't be confused with fake url address. In this case, I inserted `<img>` tag with connecting `src` attribute to `file.url` to catch the image url that I uploaded in my admin.<br><br>

And then I included my [custom model method](https://docs.djangoproject.com/en/2.2/topics/db/models/#model-methods){:target="_blank"} into my `PhotoInline` as [`readonly_fields`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.readonly_fields){:target="_blank"}.

<br>

## *orders/*

The following code is admin for *orders/*.<br>
```python
from django.contrib import admin
from . import models

@admin.register(models.Order)
class OrderAdmin(admin.ModelAdmin):

    ''' OrderAdmin Definition ''' 

    list_display = (
        'ordered_customer', 
        'ordered_product', 
        'status', 
        'created',
        'updated',
        'ordered_qty',
    )
    
    list_filter = ('status',)

    raw_id_fields = ('ordered_product', 'ordered_customer')

    search_fields = (
        'ordered_customer__email',
        'ordered_product__product_name',
    )
```
<br>

1. [`list_display`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display){:target="_blank"}: I basically included all fields. 

2. [`list_filter`](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_filter){:target="_blank"}: I included only `status` as it is critical for admin to check order status of each customers. 

3. [`raw_id_fields`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.raw_id_fields){:target="_blank"}: Choosing the model fields that are connected to [`ForeignKey`](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey){:target="_blank"} could be included in [`raw_id_fields`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.raw_id_fields){:target="_blank"}.<br>
As more and more orders are placed, it might be very hard for admin to match up with customers and products related to the orders within the dropdown menu. [`raw_id_fields`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.raw_id_fields){:target="_blank"} allows us to search [`ForeignKey`](https://docs.djangoproject.com/en/2.2/ref/models/fields/#django.db.models.ForeignKey){:target="_blank"} in separate windows. 

4. [`search_fields`](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.search_fields){:target="_blank"}: I enabled search fields with customer's email address and product's name. 


<br>


[^1]: [Markdown Options - Jekyll Documentation](https://jekyllrb.com/docs/configuration/markdown/)
[^2]: [Kramdown homepage](https://kramdown.gettalong.org/)
