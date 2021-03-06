Django Multilingual Tags
========================

A reusable Django app that allows you to add translatable tags to any other
model.

Installation
------------

To get the latest stable release from PyPi

.. code-block:: bash

    pip install django-multilingual-tags

To get the latest commit from GitHub

.. code-block:: bash

    pip install -e git+git://github.com/bitmazk/django-multilingual-tags.git#egg=multilingual_tags


Add ``multilingual_tags`` to your ``INSTALLED_APPS``

.. code-block:: python

    INSTALLED_APPS = (
        ...,
        'multilingual_tags',
    )


Don't forget to migrate your database

.. code-block:: bash

    ./manage.py migrate multilingual_tags


Usage
-----

To add tags to a model, you have to add the ``TaggedItemInline`` to
that model's admin. In your own apps, you can just do the following:

.. code-block:: python

    from django.contrib import admin

    from multilingual_tags.admin import MultilingualTagsAdminMixin

    from my_app import models

    class MyModelAdmin(admin.ModelAdmin):
        inlines = [TaggedItemInline]

    admin.site.register(models.MyModel, MyModelAdmin)

This will render the inline admin form for adding tagged items.

If you want to add tags to a third party app, you might need to import its
admin instead of Django's ``ModelAdmin`` and then unregister and re-register
the model. One way to do it would be this:

.. code-block:: python

    from django.contrib import admin

    from multilingual_tags.admin import TaggedItemInline

    from other_app.admin import SomeModelAdmin
    from other_app.models import SomeModel

    class SomeModelCustomAdmin(SomeModelAdmin):
        # be careful, if the other admin also defines admins, you need to add
        # them as well
        inlines = SomeModelAdmin.inlines + [TaggedItemInline]

    admin.site.unregister(SomeModel)
    admin.site.register(SomeModel, SomeModelCustomAdmin)


To get all the tags for an object, you can simply use the `TagManager`:

.. code-block:: python

    # Get all tags for a certain model instance
    >> Tag.objects.get_for_obj(mymodel_instance)

    [<Tag: mytag>, <Tag: myothertag>]

    # .. or get all tags for an entire queryset
    >> Tag.objects.get_for_queryset(MyModel.objects.all())

    [<Tag: mytag>, <Tag: myothertag>]



Contribute
----------

If you want to contribute to this project, please perform the following steps

.. code-block:: bash

    # Fork this repository
    # Clone your fork
    mkvirtualenv -p python2.7 django-multilingual-tags
    make develop

    git co -b feature_branch master
    # Implement your feature and tests
    git add . && git commit
    git push -u origin feature_branch
    # Send us a pull request for your feature branch
