---
title: "Composing Serializers With DRF"
date: 2018-04-01T11:52:06+01:00
draft: false
tags: [python, django, rest, meta]
categories: [python, django]
---

Django Rest Framework provides a fast way to build APIs, expressing them as a set of serializers and views. I recently
ran into a case where I wanted user-specific data to be included when a user is authenticated, but default to the
generic serializer in all other cases.

#### /api/v1/items/2 (anonymous)

```json
{
    "name": "Cannonball",
    "item_id": 2,
    "store_price": 5
}
```

#### /api/v1/items/2 (authenticated)

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

```python
from rest_framework.serializers import ModelSerializer

class ItemSerializer(ModelSerializer):
    class Meta:
        model: Item
        fields: ('name', 'item_id', 'store_price')

class ItemFavoriteSerializer(ModelSerializer):
    class Meta:
        model: Item
        fields: ('name', 'item_id', 'store_price', 'favorite')
```

Even in this simple example, there is a good deal of repetition. By composing them together we can condense the
`ItemFavoriteSerializer` to the following snippet:

```python
@composed_serializer
class ItemFavoriteSerializer(ItemSerializer)
    class Meta:
        fields: ('favorite',)
```

Now, any changes to the parent will be reflected in the child. Beautiful. The decorator is available in
[this gist](https://gist.github.com/arlyon/59e2f580bd523196791500002750ca8c).

