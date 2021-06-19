---
title: Model
pagenum: 1
---
[Model](https://docs.djangoproject.com/en/3.2/topics/db/models/#module-django.db.models){:target="_blank"} is the way of defining how our data looks like. On the web app, any data that changes will be defined in the models (e.g. price, item name, description, username, etc...)<br><br>

## TimeStampModel

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

## *users/*

In `users/` apps, we need to define our user model. Simply put, we can create our own user model inheriting `models.Model` from Django's built-in feature. 

```python
from django.db import models

class User(models.Model):
    username = models.CharField(max_length=10)
    ...
```
### Custom user model

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

    def __str__(self):
        return self.email

    class Meta: 
        verbose_name = '사용자'
        verbose_name_plural = '사용자'
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

There is another way that we can make user model, which is [`AbstractBaseUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#django.contrib.auth.models.AbstractBaseUser){:target="_blank"}. [`AbstractBaseUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#django.contrib.auth.models.AbstractBaseUser){:target="_blank"} only contains the authentication process without actual fields. To view how to make user models with [`AbstractBaseUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#django.contrib.auth.models.AbstractBaseUser){:target="_blank"}[^2]. see this post and refer to.<br><br>

With using [`AbstractBaseUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#django.contrib.auth.models.AbstractBaseUser){:target="_blank"}, I can change the authentication methods. In this case, we need to setup [`UserManager`](https://docs.djangoproject.com/en/3.2/ref/contrib/auth/#django.contrib.auth.models.UserManager) that is inherited from [`BaseUserManager`]() to determine which users will have normal/admin roles. Also, <br><br>

However, the cons for using [`AbstractBaseUser`](https://docs.djangoproject.com/en/3.2/topics/auth/customizing/#django.contrib.auth.models.AbstractBaseUser){:target="_blank"} is that it is very hard to <br><br>


### Meta class

We can provide more information (or meta data) to our model class by adding [meta class](https://docs.djangoproject.com/en/3.2/topics/db/models/#meta-options){:target="_blank"} as an inner class. I included the following options.<br><br>

#### `verbose_name`

[`verbose_name`](https://docs.djangoproject.com/en/3.2/ref/models/options/#verbose-name){:target="_blank"} provides the human-readable name of objects.<br><br>

#### `verbose_name_plural` 

[`verbose_name_plural`](https://docs.djangoproject.com/en/3.2/ref/models/options/#verbose-name-plural){:target="_blank"}, which I love to use very often, to provide human-readable name of objects. If you put those two [meta options](), you will be able to see that name of class has been changed.<br><br>


### `__str__`

[`__str__`](https://docs.djangoproject.com/en/3.2/ref/models/instances/#str){:target="_blank"} is one of the [model instance methods](https://docs.djangoproject.com/en/3.2/ref/models/instances/#other-model-instance-methods){:target="_blank"} to display object on the admin site.<br><br>

By adding the [`__str__`](https://docs.djangoproject.com/en/3.2/ref/models/instances/#str){:target="_blank"} method, we can include the human-readable representation of model from it. This can be used in many different ways.<br><br>

In this case, I chose `email` to be displayed. 


## *products/*

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

    def __str__(self):
        return f'{self.product_name}: {self.product_price}'
    
    class Meta:
        verbose_name = '상품목록'
        verbose_name_plural = '상품목록'
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


For the `product_price`, I ended up changing the field with [`DecimalField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#decimalfield){:target="_blank"} because [`DecimalField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#decimalfield){:target="_blank"} could also cover the integer number of [`IntegerField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#integerfield){:target="_blank"}[^3]. 

- `max_digits`: The total number of digits including decimal points (required)
- `decimal_places`: The number of digits of decimal places (required). 

If you put bigger number that exceeds the `max_digits`, then Django will throw error as below:

> *InvalidOperation at /admin/orders/order [<class 'decimal.InvalidOperation'>]*

<!-- {% include alerts/warning.html content="If you put bigger number that exceeds the max_digits, then Django will throw InvalidOperation [<class 'decimal.InvalidOperation'>] error." %} -->

<br>

For the `product_description`, I chose [`TextField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#textfield){:target="_blank"} because I did not want admin be restricted to text limit of explanation of products.<br><br>

For `is_onsale`, `is_newarrival` and `is_preorder`, I chose [`BooleanField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#booleanfield){:target="_blank"} just to simply show True/False value. These fields will be used in *admin.py* to be shown in admin panel as well.<br><br>

Meanwhile, I included the [`__str__`](https://docs.djangoproject.com/en/3.2/ref/models/instances/#str){:target="_blank"} model instance method as I did in `users/` part above.
<br><br>

### `save()` method

For the `product_color` and `product_size`, when admin put data in English, I wanted data to be saved in uppercase letters so that admin can distinguish clearly.<br><br>

In order to do this, I used [overriding predefined model methods](https://docs.djangoproject.com/en/2.2/topics/db/models/#overriding-predefined-model-methods){:target="_blank"} and used [`save()`](https://docs.djangoproject.com/en/2.2/ref/models/instances/#django.db.models.Model.save){:target="_blank"}([<span style="font-style: italic; font-weight: bold; color: #e83e8b;">source</span>](https://docs.djangoproject.com/en/2.2/_modules/django/db/models/base/#Model.save){:target="_blank"}) method.<br><br>

Unlike when you [make changes and save in admin model](https://docs.djangoproject.com/en/2.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.save_model){:target="_blank"}([<span style="font-style: italic; font-weight: bold; color: #e83e8b;">source</span>](https://docs.djangoproject.com/en/2.2/_modules/django/contrib/admin/options/#ModelAdmin.save_model){:target="_blank"}), making changes in models will affect all aspects including admin panel, views, console and etc.<br><br>

I added the following code more below the `Product` model and above `__str__` method.<br>
```python
def save(self, *args, **kwargs):
    self.product_color = str.upper(self.product_color)
    self.product_size = str.upper(self.product_size)
    super().save(*args, **kwargs)
```
<br>

With using `str.upper()` method, I made `product_color` and `product_size` should always be uppercase letters.<br><br>

Moreover, I used `super().save(*args, **kwrags)`[^4] as exactly shown in Django documentation so that any changes made on models could be saved in admin panel as well. 

## *orders/*

Orders are all about information of orders. I named model name as `Order`, and it looks as follows: 

```python
from common.models import TimeStampModel

class Product(TimeStampModel):

    ''' Order Model Definition '''

    # Omitted
    
    order_id = models.CharField(max_length=10, verbose_name='주문번호')
    status = models.CharField(max_length=30, verbose_name='주문상태', choices=STATUS_CHOICES, default=AWAITING_PAYMENT)
    ordered_customer = models.OneToOneField(Shipping, on_delete=models.CASCADE, verbose_name='주문고객', parent_link=True)
    ordered_product = models.ForeignKey('products.Product', on_delete=models.CASCADE, verbose_name='주문상품')
    ordered_qty = models.PositiveIntegerField(verbose_name='주문수량')

    def __str__(self):
        return str(self.order_id)
    
    class Meta:
        verbose_name = '주문내역'
        verbose_name_plural = '주문내역'
```
<br>

The followings are the description of each model name:<br>

| Field Name| Description | 
| :--- |    :----   | 
| `order_id` | Unique ID order number. Linked with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield){:target="_blank"}| 
| `status`| Status of orders. Linked with [`CharField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#charfield){:target="_blank"}| 
| `ordered_customer`| Customer who placed order. Linked with [`OneToOneField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#onetoonefield){:target="_blank"}| 
| `ordered_product`| Products that have been ordered. Linked with [`ForeignKey`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#foreignkey){:target="_blank"}| 
| `ordered_qty`| Total quantity that customer ordered. Linked with [`PositiveIntegerField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#positiveintegerfield){:target="_blank"}| 

<br>

For the `ordered_qty` field, I used [`PositiveIntegerField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#positiveintegerfield){:target="_blank"} because I did not want `ordered_qty` input to be below than 0. Although I will setup in views that users cannot input number below than 0, I just wanted to prevent this from backend side also.<br><br>

Meanwhile, it looks like we need to discuss about the [relationship fields](https://docs.djangoproject.com/en/3.2/ref/models/fields/#module-django.db.models.fields.related){:target="_blank"} for the first time.<br><br>

[Relationship fields](https://docs.djangoproject.com/en/3.2/ref/models/fields/#module-django.db.models.fields.related){:target="_blank"} are used when we are to link fields from other models' fields. We need to use different relationship fields based on how the relationship between two models' fields look like.<br><br>

### `OneToOneField`

As you can literally can tell from its name, [`OneToOneField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#onetoonefield){:target="_blank"} is used when you would like to link up with one field to another one field. When models are on relationship of "1:1", then [`OneToOneField`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#onetoonefield){:target="_blank"} would be the right choice.<br><br>

For instance, in this case, I regarded as `ordered_customer` is as same as the person who is defined in `Shipping` model, and they are all __unique__, which means it can't indicate other people.<br><br>


### `ForeignKey`

[`ForeignKey`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#foreignkey){:target="_blank"} is used when fields are on relationship of "1:N", which means one model field can have multiple model field that is connected with [`ForeignKey`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#foreignkey){:target="_blank"}. For example, one blog post can have multiple comments, but each comments cannot belong to other blog posts. That is, comments cannot be shared with other blog posts.<br><br>

In this case, one `User` can have multiple `Order`s if he/she wants. However, that specific `Order` cannot be other `User`'s.<br><br>

`ordered_product` is connected to `users.User` using [`ForeignKey`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#foreignkey){:target="_blank"}. Also, I set [`on_delete`](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.ForeignKey.on_delete){:target="_blank"} argument (which is required) as [`models.CASCADE`](), which means if `User` has been deleted, then its `Order` will also be deleted.<br><br><br><br>


Now that we are finished with making models, let's move on to the next section: admin! 

## References & Notes

[^1]: Indicates to the 

[^2]: [Extending User Model Using a Custom Model Extending AbstractBaseUser](https://simpleisbetterthancomplex.com/tutorial/2016/07/22/how-to-extend-django-user-model.html){:target="_blank"}

[^3]: [Which field is best for USD currency?](https://stackoverflow.com/questions/1139393/what-is-the-best-django-model-field-to-use-to-represent-a-us-dollar-amount){:target="_blank"}

[^4]: See this [post](https://stackoverflow.com/questions/576169/understanding-python-super-with-init-methods){:target="_blank"} for more information about `super()` in Python.
