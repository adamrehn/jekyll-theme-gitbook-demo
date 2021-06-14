---
title: Model
pagenum: 1
---
[Model](https://docs.djangoproject.com/en/3.2/topics/db/models/#module-django.db.models){:target="_blank"} is the way of defining how our data looks like. On the web app, any data that changes will be defined in the models (e.g. price, item name, description, username, etc...)<br><br>

### TimeStampModel

I believed all model fields should include common data which are __date and time__. For instance, when admin uploaded new products in admin panel, they might want to get information when did they upload the products. It is regardless to say that admin should know when did customer purchase the product they want.<br><br>

Thus, I decided to create `TimeStampModel` in `commons/` app, so that I can use this model that can be shared with all models in each apps. Fields that are included in `TimeStampModel` are:<br>

1. `created`: This is the field that shows when item was __created__. This is created with [`DateTimeField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#datetimefield){:target="_blank"}. For the field option, [`auto_now_add`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.DateField.auto_now_add){:target="_blank"} is connected.<br>

2. `updated`: This is the field that shows when item was __updated__ or __saved__. This is also connected with [`DateTimeField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#datetimefield){:target="_blank"}. For the field option, [`auto_add`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.DateField.auto_now){:target="_blank"} is connected.<br><br>

Final `TimeStampModel` looks like this:<br>

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

Meanwhile, I did not want to upload `created` and `updated` information into my DB, or it will look too messy in admin panel. Therefore, I used [meta class](https://docs.djangoproject.com/en/3.2/topics/db/models/#meta-options){:target="_blank"} and included [`abstract = True`](https://docs.djangoproject.com/en/3.2/ref/models/options/#abstract){:target="_blank"} so that model will be an [abstract-base class](https://docs.djangoproject.com/en/3.2/topics/db/models/#abstract-base-classes){:target="_blank"}.<br><br>

Now, I can use `TimeStampModel` in everywhere in each apps' models. To do this, I could simply include the code `from commons.models import TimeStampModel` at the very top of each apps' models.

{% include alerts/info.html content="Comment '''{ModelName} Definition ''' is the conventional way to notify people with model definition." %}

<br>

### *users/*

In `users/` apps, we need to define our user model. Simply put, we can create our own user model inheriting `models.Model` from Django's built-in feature. 

```python
from django.db import models

class User(models.Model):
    username = models.CharField(max_length=10)
    ...
```

Sometimes, however, we may want to customize our own user model. This could be useful when, for instance, we want to make user login(signin) with email address so that they can easily reset password if they lost it.<br><br>


#### `AbstractUser`

When you want to make customized models, you may want to use [`AbstractUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project){:target="_blank"} model, because [`AbstractUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project){:target="_blank"} literally gives you everything!<br><br>

For User model, I decided to use [`AbstractUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project){:target="_blank"} so that I can have customized models with built-in fields. My custom `User` model looks as below:<br><br>


```python
from django.contrib.auth.models import AbstractUser

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

I also included few more fields which are `language` and `currency`. This is because later I wanted to prepare for the internationalization(i18n) for foreign users (any users coming from outside of Korea). Both model fields are created with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield){:target="_blank"} with `max_length` of 10, which is required arguments to put. I also included the following [field options](https://docs.djangoproject.com/en/3.2/ref/models/fields/#field-options){:target="_blank"} further.<br>

1. [`choices`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#choices){:target="_blank"}: So that admin can only choose specific categories they wants. I did not want to make extra separate models just for this. I set `default` value as `KOREAN` for `language` and `KRW` for `currency` respectively, because I am starting off with Korean version first. 
2. [`verbose_name`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#verbose-name){:target="_blank"}: So as to provide human-readable field names for admin in admin panel.

<br>

After including the `AbstractUser`, however, make sure you need to include `AUTH_USER_MODEL` in `settings.py`. This will help you override[^1] the <br><br>

At the very bottom of `settings.py`, include <br>
```python
AUTH_USER_MODEL = 'users.User'
```

If you do not include `AUTH_USER_MODEL` as above, then it will throw an error as below: 

> users.User.user_permissions: (fields.E304) Reverse accessor for 'User.user_permissions' clashes with reverse accessor for 'User.user_permissions'.

<!-- {% include alerts/warning.html content="If you don't set AUTH_USER_MODEL, it will throw error of users.User.user_permissions: (fields.E304) error" %} -->

<br>

#### `AbstractBaseUser`

There is another way that we can make user model, which is [`AbstractBaseUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#django.contrib.auth.models.AbstractBaseUser){:target="_blank"}. `AbstractBaseUser` can be used when we would like to let user login with their email, NOT username. Also, it allows us to develop authentication process other than login. This could be useful when we would like to retrieve user information directly from other projects.<br><br>

However, the cons for using `AbstractBaseUser` is that it is very hard to 

### *products/*

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
    product_price = models.DecimalField(max_digits=8, decimal_places=2, verbose_name='상품가격')
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
| `product_name` | Name of product. Linked with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield){:target="_blank"}| 
| `product_price`| Price of product. Linked with [`DecimalField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#decimalfield){:target="_blank"}| 
| `product_description`| Description of product. Linked with [`TextField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#textfield){:target="_blank"}| 
| `product_color`| Color of product. Linked with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield){:target="_blank"}| 
| `product_size`| Size of product. Linked with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield){:target="_blank"}| 
| `is_onsale`| Whether or not product is on sale. Linked with [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield){:target="_blank"}| 
| `is_newarrival`| Whether or not product is new arrival. Linked with [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield){:target="_blank"}| 
| `is_preorder`| Whether or not product is new arrival. Linked with [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield){:target="_blank"}| 
| `customer`| Whether or not product is new arrival. Linked with [`ForeignKey`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.ForeignKey){:target="_blank"}| 


For the `product_price`, I ended up changing the field with [`DecimalField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#decimalfield){:target="_blank"} because [`DecimalField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#decimalfield){:target="_blank"} could also cover the integer number of [`IntegerField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#integerfield){:target="_blank"}. 

- `max_digits`: The total number of digits including decimal points (required)
- `decimal_places`: The number of digits of decimal places (required). 

If you put bigger number that exceeds the `max_digits`, then Django will throw error as below:

> InvalidOperation at /admin/orders/order [<class 'decimal.InvalidOperation'>]

<!-- {% include alerts/warning.html content="If you put bigger number that exceeds the max_digits, then Django will throw InvalidOperation [<class 'decimal.InvalidOperation'>] error." %} -->

<br>

For the `product_description`, I chose [`TextField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#textfield){:target="_blank"} because I did not want admin be restricted to text limit of explanation of products.<br><br>

For `is_onsale`, `is_newarrival` and `is_preorder`, I chose [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield){:target="_blank"} just to simply show True/False value. These fields will be used in *admin.py* to be shown in admin panel as well.<br><br>

#### `ForeignKey`

`customer` field literally means customer who purchased the products. This `customer` is not different from the user that is defined in `User` model in previous section. Therefore, I needed to connect them together, which could be done by using [`ForeignKey`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#foreignkey){:target="_blank"}.<br><br>

Relation between `Product` and `User` is as follows:
- `User` can have many `Products`. 
- However, `Products` cannot have many `Users`.




## Alerts

These custom alerts ship with the theme, but you can also use the [alert classes provided by Bootstrap](https://getbootstrap.com/docs/4.5/components/alerts/){:target="_blank"} if you prefer those instead.






## References & Notes
[^1]: Indicates how you will be able to 
[^2]: [Which field is best for USD currency?](https://stackoverflow.com/questions/1139393/what-is-the-best-django-model-field-to-use-to-represent-a-us-dollar-amount){:target="_blank"}

