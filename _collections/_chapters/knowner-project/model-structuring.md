---
title: Model
pagenum: 1
---
# Model
[Model](https://docs.djangoproject.com/en/3.2/topics/db/models/#module-django.db.models) is the way of defining how our data looks like. On the web app, any data that changes will be defined in the models (e.g. price, item name, description, username, etc...)<br><br>

### TimeStampModel

I believed all model fields should include common data which are __date and time__. For instance, when admin uploaded new products in admin panel, they might want to get information when did they upload the products. It is regardless to say that admin should know when did customer purchase the product they want.<br><br>

1. `created`: This is the field that shows when item was __created__. This is created with [`DateTimeField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#datetimefield). For the field option, [`auto_now_add`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.DateField.auto_now_add) is connected.<br>

2. `updated`: This is the field that shows when item was __updated__ or __saved__. This is also connected with [`DateTimeField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#datetimefield). For the field option, [`auto_add`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.DateField.auto_now) is connected.<br><br>

`TimeStampModel` looks like this:<br><br>

```python
from django.db import models

class TimeStampModel(models.Model):

    ''' TimeStampModel Definition ''' 

    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)

    class Meta: 
        abstract = True 
```
<br>

Meanwhile, I did not want to upload `created` and `updated` information into my DB, or it will look too messy in admin panel. Therefore, I used [meta class](https://docs.djangoproject.com/en/3.2/topics/db/models/#meta-options) and included [`abstract = True`](https://docs.djangoproject.com/en/3.2/ref/models/options/#abstract) so that model will be an [abstract-base class](https://docs.djangoproject.com/en/3.2/topics/db/models/#abstract-base-classes).<br>

Now, I can use `TimeStampModel` in everywhere in each apps' models. To do this, I could simply include the code `from commons.models import TimeStampModel` at the very top of each apps' models.

### `users/`

#### `AbstractUser`

When you want to make customized models, you may want to use [`AbstractUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project) model, because [`AbstractUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project) literally gives you everything!<br><br>

For User model, I decided to use [`AbstractUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project) so that I can have customized models with built-in fields. My custom `User` model looks as below:<br><br>


```python
class User(AbstractUser):

    ''' Customized User Model '''

    KOREAN = 'KR'
    ENGLISH = 'EN'
    LANGUAGE_CHOICES = [
        (KOREAN, 'Korean'),
        (ENGLISH, 'English'),
    ]

    KRW = 'KRW'
    USD = 'USD'
    CURRENCY_CHOICES = [
        (KRW, 'Korean Won'),
        (USD, 'US Dollar')
    ]
    
    language = models.CharField(choices=LANGUAGE_CHOICES, max_length=10, verbose_name='Preferred Language', default=KOREAN)
    currency = models.CharField(choices=CURRENCY_CHOICES, max_length=10, verbose_name='Currency', default=KRW)
```
<br>

I also included few more fields which are `language` and `currency`. This is because later I wanted to prepare for the internationalization(i18n) for foreign users (any users coming from outside of Korea). Both model fields are created with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield) with `max_length` of 10, which is required arguments to put. I also included the following [field options](https://docs.djangoproject.com/en/3.2/ref/models/fields/#field-options) further.<br>

1. [`choices`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#choices): So that admin can only choose specific categories they wants. I did not want to make extra separate models just for this.
2. [`verbose_name`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#verbose-name): So as to provide human-readable field names for admin in admin panel.

{% include alerts/info.html content="Comment '''{ModelName} Definition ''' is the common way that notifies people with model definition." %}


### `products/`

Products literally include the information of products. I named model name as `Product`, and it looks as follows: 

```python
from common.models import TimeStampModel

class Product(TimeStampModel):

    ''' Product Model Definition ''' 

    TOPS = 'Tops'
    BOTTOMS = 'Bottoms'
    PRODUCT_CATEGORY = [
        (TOPS, 'Tops'),
        (BOTTOMS, 'Bottoms'),
    ]

    product_name = models.CharField(max_length=80, verbose_name='상품이름')
    product_price = models.IntegerField(verbose_name='상품가격')
    product_description = models.TextField(verbose_name='상품설명')
    product_color = models.CharField(max_length=10, verbose_name='색상', null=True, blank=True)
    product_size = models.CharField(max_length=2, verbose_name='사이즈', null=True, blank=True)
    product_category = models.CharField(max_length=30, verbose_name='상품 카테고리', choices=PRODUCT_CATEGORY, default=TOPS)
    is_onsale = models.BooleanField(verbose_name='할인 중', default=False)
    is_newarrival = models.BooleanField(verbose_name='신상', default=False)
    is_preorder = models.BooleanField(verbose_name='프리오더', default=False)
    customer = models.ForeignKey('users.User', on_delete=models.SET_NULL, null=True)
```
<br>

As I mentioned previously on `TimeStampModel` part, I included it for chasing when did admin created and updated the product information.<br><br>

The followings are the description of each model name:<br>

| Field Name| Description | 
| :--- |    :----   | 
| `product_name` | Name of product. Linked with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield)| 
| `product_price`| Price of product. Linked with [`IntegerField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#integerfield)| 
| `product_description`| Description of product. Linked with [`TextField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#textfield)| 
| `product_color`| Color of product. Linked with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield)| 
| `product_size`| Size of product. Linked with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield)| 
| `is_onsale`| Whether or not product is on sale. Linked with [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield)| 
| `is_newarrival`| Whether or not product is new arrival. Linked with [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield)| 
| `is_preorder`| Whether or not product is new arrival. Linked with [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield)| 
| `customer`| Whether or not product is new arrival. Linked with [`ForeignKey`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.ForeignKey)| 


I was quite confused which model field should I use for `product_price`. According to my research, [`DecimalField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#decimalfield) would be best choice for USD currency[^1]. However, as I was working on KRW currency first, I ended up deciding [`IntegerField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#integerfield).<br><br>

For the `product_description`, I chose [`TextField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#textfield) because I did not want admin be restricted to text limit of explanation of products.<br><br>

For `is_onsale`, `is_newarrival` and `is_preorder`, I chose [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield) just to simply show True/False value. These fields will be used in `admin.py` to be shown in admin panel as well.<br><br>

#### `ForeignKey`

`customer` field literally means customer who purchased the products. This `customer` is not different from the user that is defined in `User` model in previous section. Therefore, I needed to connect them together, which could be done by using [`ForeignKey`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#foreignkey).<br><br>

Relation between `Product` and `User` is as follows:
- `User` can have many `Products`. 
- However, `Products` cannot have many `Users`. 





## Alerts

These custom alerts ship with the theme, but you can also use the [alert classes provided by Bootstrap](https://getbootstrap.com/docs/4.5/components/alerts/) if you prefer those instead.



{% include alerts/warning.html content="This is a warning alert." %}



## References
[^1]: [Which field is best for USD currency](https://stackoverflow.com/questions/1139393/what-is-the-best-django-model-field-to-use-to-represent-a-us-dollar-amount)

