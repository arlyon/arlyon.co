---
title: "Composing Serializers With DRF"
date: 2018-04-01T12:17:09+01:00
draft: false
tags: ["gist", "code", "python", "drf"]
categories: ["Gists"]
user: "arlyon"
gist: "59e2f580bd523196791500002750ca8c"
# last two are used in schema.org/SoftwareSourceCode
language: "Python"
runtime: "Python 3.6"
---

Django Rest Framework provides a fast way to build APIs, expressing them as a set of serializers and views. I recently
ran into a case where I wanted user-specific data to be included when a user is authenticated, but default to the
generic serializer in all other cases.

### /api/v1/items/2 (anonymous)

```json
{
    "name": "Cannonball",
    "item_id": 2,
    "store_price": 5
}
```

### /api/v1/items/2 (authenticated)

```json
{
    "name": "Cannonball",
    "item_id": 2,
    "store_price": 5,
    "favorited": true
}
```

This lead to two separate but similar serializers, only differing with the inclusion of the `favorited` field. In
Python composition inherits fields from both parents but patching DRF's Meta class is a little difficult since it can't
reach into the parent class to fetch the (potentially inherited) list of fields. But, fear not, as we're about to do
some meta-programming (pun intended). In short, we can use a decorator to generate a new class with the correct
information in the Meta class, saving some headache and keeping things nice and DRY in the process.

For reference, here are our old serializers:

### serializers.py

```python
from rest_framework.serializers import ModelSerializer

class ItemSerializer(ModelSerializer):
    class Meta:
        model: Item
        fields: ('name', 'item_id', 'description')

class ItemPriceSerializer(ModelSerializer):

    price = SerializerMethodField()

    def get_price(item):
        ...

    class Meta:
        fields = ('name', 'item_id', 'description', 'price')

class ItemFavoriteSerializer(ModelSerializer):
    class Meta:
        model: Item
        fields: ('name', 'item_id', 'description', 'favorite')

class ItemPriceFavoriteSerializer(ItemPriceSerializer):
    class Meta:
        model: Item
        fields: ('name', 'item_id', 'description', 'favorite', 'price')
```

Even in this simple example, there is a good deal of repetition. By composing them together we can condense the
the last three serializers down considerably:

### serializers.py (new)

```python
@composed_serializer
class ItemPriceSerializer(ItemSerializer):

    price = SerializerMethodField()

    def get_price(item):
        ...

    class Meta:
        fields: ('price',)

@composed_serializer
class ItemFavoriteSerializer(ItemSerializer):
    class Meta:
        fields: ('favorite',)

@composed_serializer
class ItemPriceFavoriteSerializer(ItemPriceSerializer, ItemFavoriteSerializer):
    pass
```

The decorator works by combining the Meta class for each of the parent serializers as well as adding the fields defined
in the Meta for the child class. Additionally, all the behaviour from normal python inheritance applies such as
the function defined in the `ItemPriceSerializer`. Now, any changes to the parents will be correctly reflected in the
child. Simple.

