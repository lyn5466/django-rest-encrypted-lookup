django-rest-encrypted-lookup
=============

django-rest-encrypted-lookup provides replacement ViewSets, Serializers, and Fields that replace your IntegerField pk or id lookups with encrypted string lookups.

For example, the json representation of a poll goes from:

```
    {
        "id": 5,
        "questions": [
                      2,
                      10,
                      12,
        ]
    
    }
```
to:
```
    {
        "id": "4xoh7gja2mtvz3i47ywy5h6ouu",
        "questions": [
                      "t6y4zo26vutj4xclh5hpy3sc5m",
                      "hp7c75ggggiwv6cs5zc4mpzeoe",
                      "rqq5a2evfokyo7tz74loiu3bcq",
        ]
    
    }
```

The endpoint:

```
    /api/polls/5/
```
becomes:
```
    /api/polls/4xoh7gja2mtvz3i47ywy5h6ouu/
```

Encrypted lookups are *not* appropriate to use as a security measure against users guessing object URIs. Encrypted
lookups *are* appropriate to use as a non-security-critical method of obfuscating object pks/ids.


Installation
===============

Install the module in your Python distribution or virtualenv:

    $ pip install git+https://github.com/SigmaEducation/django-rest-encrypted-lookup.git

Add the application to your `INSTALLED_APPS`:

```
  INSTALLED_APPS = (
  ...
  "rest_framework_encrypted_lookup",
  ...
  )
```

And in settings.py:

```
  LOOKUP_FIELD = 'id'  # String value name of your drf lookup field, generally 'id' or 'pk'
```

How it Works
============

Encrypted lookup strings are *not* stored in the database in association with the objects they represent. Encrypted
lookups are generated by the model serializers during response composition. Encrypted lookups presented in the endpoint
URI are decrypted in the call to dispatch, and encrypted lookups presented in data fields are decrypted by the model
deserializers.

Use
===

django-rest-encrypted-lookup provides two field classes:

    from rest_framework_encrypted_lookup.fields import EncryptedLookupField, EncryptedLookupRelatedField
    
a new serializer class:

    from rest_framework_encrypted_lookup.serializers import EncryptedLookupModelSerializer
    
and a generic view class:

    from rest_framework_encrypted_lookup.views import EncryptedLookupGenericViewSet
    
Use these in place of the classes provided with Django Rest Framework 3.
    
Example:

``` 
    # models.py
    ...
    class Poll(models.Model):
        ...
        
        
    # serializers.py
    ...
    class PollSerializer(EncryptedLookupModelSerializer):

        class Meta:
            model = Poll
            
            
    # views.py
    ...
    from rest_framework import viewsets
    
    class PollViewSet(EncryptedLookupGenericViewSet,
                      viewsets.mixins.ListModelMixin,
                      viewsets.mixins.RetrieveModelMixin,
                      ...
                     )
        
        queryset = Poll.objects.all()
        serializer_class = PollSerializer
        
        lookup_field = 'id'
```

Of the four classes included in this package, the example above makes use of `EncryptedLookupModelSerializer`, and 
`EncryptedLookupGenericViewSet`.

The fields `EncryptedLookupField`, `EncryptedLookupRelatedField` are used implicitly
by the EncryptedLookupModelSerializer. These fields may also be used explicitly, if needed.

Compatibility
=============

* Django Rest Framework 3.0.1
* Django 1.7
* Python 3.4

Todo includes the use of tox to test compatibility against multiple requirement versions.

Additional Requirements
=======================

* PyCrypto 2.6.1


Getting Involved
================

Feel free to open pull requests or issues. GitHub is the canonical location of this project.
