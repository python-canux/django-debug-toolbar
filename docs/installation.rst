Installation
============

Each of the following steps needs to be configured for the Debug Toolbar to be
fully functional.

Getting the code
----------------

The recommended way to install the Debug Toolbar is via pip_::

    $ python -m pip install django-debug-toolbar

If you aren't familiar with pip, you may also obtain a copy of the
``debug_toolbar`` directory and add it to your Python path.

.. _pip: https://pip.pypa.io/

To test an upcoming release, you can install the in-development version
instead with the following command::

     $ python -m pip install -e git+https://github.com/jazzband/django-debug-toolbar.git#egg=django-debug-toolbar

Prerequisites
-------------

Make sure that ``'django.contrib.staticfiles'`` is `set up properly
<https://docs.djangoproject.com/en/stable/howto/static-files/>`_ and add
``'debug_toolbar'`` to your ``INSTALLED_APPS`` setting::

    INSTALLED_APPS = [
        # ...
        'django.contrib.staticfiles',
        # ...
        'debug_toolbar',
    ]

    STATIC_URL = '/static/'

If you're upgrading from a previous version, you should review the
:doc:`change log <changes>` and look for specific upgrade instructions.

Setting up URLconf
------------------

Add the Debug Toolbar's URLs to your project's URLconf::

    import debug_toolbar
    from django.conf import settings
    from django.urls import include, path

    urlpatterns = [
        ...
        path('__debug__/', include(debug_toolbar.urls)),
    ]

This example uses the ``__debug__`` prefix, but you can use any prefix that
doesn't clash with your application's URLs. Note the lack of quotes around
``debug_toolbar.urls``.

Enabling middleware
-------------------

The Debug Toolbar is mostly implemented in a middleware. Enable it in your
settings module as follows::

    MIDDLEWARE = [
        # ...
        'debug_toolbar.middleware.DebugToolbarMiddleware',
        # ...
    ]

.. warning::

    The order of ``MIDDLEWARE`` is important. You should include the Debug
    Toolbar middleware as early as possible in the list. However, it must come
    after any other middleware that encodes the response's content, such as
    :class:`~django.middleware.gzip.GZipMiddleware`.

.. _internal-ips:

Configuring Internal IPs
------------------------

The Debug Toolbar is shown only if your IP address is listed in the
:django:setting:`INTERNAL_IPS` setting.  This means that for local
development, you *must* add ``'127.0.0.1'`` to :django:setting:`INTERNAL_IPS`;
you'll need to create this setting if it doesn't already exist in your
settings module::

   INTERNAL_IPS = [
       # ...
       '127.0.0.1',
       # ...
   ]

You can change the logic of determining whether or not the Debug Toolbar
should be shown with the :ref:`SHOW_TOOLBAR_CALLBACK <SHOW_TOOLBAR_CALLBACK>`
option.  This option allows you to specify a custom function for this purpose.

Troubleshooting
---------------

On some platforms, the Django ``runserver`` command may use incorrect content
types for static assets. To guess content types, Django relies on the
:mod:`mimetypes` module from the Python standard library, which itself relies
on the underlying platform's map files. If you find improper content types for
certain files, it is most likely that the platform's map files are incorrect or
need to be updated. This can be achieved, for example, by installing or
updating the ``mailcap`` package on a Red Hat distribution, ``mime-support`` on
a Debian distribution, or by editing the keys under ``HKEY_CLASSES_ROOT`` in
the Windows registry.
