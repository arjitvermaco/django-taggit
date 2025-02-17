Changelog
=========

(Unreleased)
~~~~~~~~~~~~

* Add an admin command to remove orphaned tags
* Remove support for Python 3.8
* Fix an issue where the admin merge tag form redirect would fail when querystrings are present inside the URL

6.1.0 (2024-09-29)
~~~~~~~~~~~~~~~~~~

* Add a management command (``remove_orphaned_tags``) to remove orphaned tags
* Add a fallback for when multiple tags are found in case-insensitivity mode (the earliest by PK is returned)
* Add a ``deduplicate_tags`` management command to remove duplicate tags based on case insensitivity. This feature is enabled when ``TAGGIT_CASE_INSENSITIVE`` is set to ``True`` in the settings.
* We no longer package tests, docs, or the sample taggit app into the distributed wheels. While we believe this shouldn't affect anything for users of the library, please tell us if you find yourself hitting issues (like around import errors)
* Fix missing template file for admin merge tag action

6.0.0 (2024-07-27)
~~~~~~~~~~~~~~~~~~

* By default, order tag items on instances by the primary key. This generally means that they will be ordered by "creation date" for the tag item.
  The previous behavior for this was that by default tag items were not ordered. In practice tag items often end up ordered by creation date anyways, just due to how databases work, but this was not a guarantee.
  If you wish to have the old behavior, set ``ordering=[]`` to your ``TaggableManager`` instance.
  We believe that this should not cause a noticable performance change, and the number of queries involved should not change.
* Added the ability to merge tags via the admin
* Add Django 5.0 support (no code changes were needed, but now we test this release).
* Add Python 3.12 support
* Add support for dumpdata/loaddata using natural keys

5.0.1 (2023-10-26)
~~~~~~~~~~~~~~~~~~

* Fix the package metadata to properly reflect the right Django and Python version requirements
  Release 5.0.0 improperly stated its Django bounds as >=3.2, so people installing without bounds will end up on a version that won't work.

5.0.0 (2023-10-24)
~~~~~~~~~~~~~~~~~~
* **Backwards icompatible:** Rename the (``content_type``, ``object_id``) index on ``TaggedItem``.
  It is very unlikely for this to affect your code itself, and a migration will rename the index. This should not cause any downtime according to my research (Postgres does not lock the table for index renames, and Oracle holds a tiny lock to do it, and the change is only to the metadata, so is not dependent on table size).

* **Backwards incompatible:** Remove the ``.index_together`` and ``.unique_together`` attributes on ``TaggedItem``

  We are instead using ``constraints`` and ``indexes`` to set up these properties.
* Remove support for Django 3.2.
* Remove usage of deprecated APIs for Django 4.2
* Remove support for Python 3.7 (no code changes involved)
* Fix ``tag_kwargs`` and ``TAGGIT_CASE_INSENSITIVE=True`` discrepency.

4.0.0 (2023-05-04)
~~~~~~~~~~~~~~~~~~
* Remove Python 3.6 support (no code changes occurred, but we no longer test this release).
* Remove Django 4.0 support (no code changes occurred, but we no longer test this release).
* Add Django 4.2 support.

3.1.0 (2022-11-08)
~~~~~~~~~~~~~~~~~~

* Add Python 3.11 support (no code changes were needed, but now we test this release).
* Add Django 4.1 support (no code changes were needed, but now we test this release).
* Fixed an issue where object caches would not be properly cleared after updating tags, leading
  to stale reads in cases where ``prefetch_related`` is used.
* Change ``TagListSerializerField`` to be a subclass of ``ListField``. This should improve support for API document generation. This change should not affect API behavior, but might affect metaprogramming code, so please procede carefully during this update.

3.0.0 (2022-05-02)
~~~~~~~~~~~~~~~~~~

* **Backwards incompatible:** Tag slugification used to silently strip non-ASCII characters
  from the tag name to make the slug. This leads to a lot of confusion for anyone using
  languages with non-latin alphabets, as well as weird performance issues.

  Tag slugification will now, by default, maintain unicode characters as-is during
  slugification. This will lead to less surprises, but might cause issues for you if you are
  expecting all of your tag slugs to fit within a regex like ``[a-zA-Z0-9]`` (for example in
  URL routing configurations).

  Generally speaking, this should not require action on your part as a library user, as
  existing tag slugs are persisted in the database, and only new tags will receive the
  enhanced unicode-compatible slug.

  If you wish to maintain the old stripping behavior, set the setting
  ``TAGGIT_STRIP_UNICODE_WHEN_SLUGIFYING`` to ``True``.

  As a reminder, custom tag models can easily customize slugification behavior by overriding
  the ``slugify`` method to your business needs.

`` Drop Django 2.2 support.

2.1.0 (2022-01-24)
~~~~~~~~~~~~~~~~~~

* Add Python 3.10 support.
* Add Django 4.0 support.
* Drop Django 3.1 support.


2.0.0 (2021-11-14)
~~~~~~~~~~~~~~~~~~

* **Backwards incompatible:** ``TaggableManager.set`` now takes a list of tags
  (instead of varargs) so that its API matches Django's ``RelatedManager.set``. Example:

  - previously: ``item.tags.set("red", "blue")``
  - now: ``item.tags.set(["red", "blue"])``

* Fix issue where ``TagField`` would incorrectly report that a field has changed on empty values.
* Update Russian translation.
* Add Persian translation
* Fix issue for many languages where content types were not being properly translated.
* Provide translators additional context regarding strings in TagBase model.


1.5.1 (2021-07-01)
~~~~~~~~~~~~~~~~~~

* Fix compiled Ukranian translation (which would cause a failure on load for this locale).
* Update compiled Danish translation.


1.5.0 (2021-06-30)
~~~~~~~~~~~~~~~~~~

* Vendor in the `django-taggit-serializer` project (under `taggit.serializers`).
* Add Arabic translation.
* Add Ukranian translation.


1.4.0 (2021-04-19)
~~~~~~~~~~~~~~~~~~

* Add Python 3.9 support.
* Remove Python 3.5 support.
* Add Django 3.2 support.
* Remove Django 1.11 and 3.0 support.
* Add Danish translation.
* Fix crashing that could occur with ``similar_objects`` in multi-inheritance contexts.
* Add support for custom fields on through table models with `through_defaults` for ``TaggedManager.add`` and ``TaggedManager.set``.


1.3.0 (2020-05-19)
~~~~~~~~~~~~~~~~~~

* Model and field ``verbose_name`` and ``verbose_name_plural`` attributes are
  now lowercase. This simplifies using the name in the middle of a sentence.
  When used as a header, title, or at the beginning of a sentence, a text
  transformed can be used to adjust the case.
* Fix prefetch_related when using UUIDTaggedItem.
* Allow for passing in extra constructor parameters when using
  ``TaggableManager.add``. This is especially useful when using custom
  tag models.

1.2.0 (2019-12-03)
~~~~~~~~~~~~~~~~~~

* **Removed** support for end-of-life Django 2.0 and 2.1.
* Added support for Django 3.0.
* Added support for Python 3.8.
* Moved ``TaggedItemBase.tags_for()`` to ItemBase.
* Replaced reference to removed Django's ``.virtual_fields`` with
  ``.private_field``.
* Added ``TextareaTagWidget``.

1.1.0 (2019-03-22)
~~~~~~~~~~~~~~~~~~

* Added Finnish translation.
* Updated Chinese translation.
* Updated Esperanto translation.
* Fix ``form.changed_data`` to allow early access for a tags defined with
  ``blank=True``.

1.0.0 (2019-03-17)
~~~~~~~~~~~~~~~~~~

* **Backwards incompatible:** Remove support for Python 2.
* Added ``has_changed()`` method to ``taggit.forms.TagField``.
* Added multi-column unique constraint to model ``TaggedItem`` on fields
  ``content_type``, ``object_id``, and ``tag``. Databases that contain
  duplicates will need to add a data migration to resolve these duplicates.
* Fixed ``TaggableManager.most_common()`` to always evaluate lazily. Allows
  placing a ``.most_common()`` query at the top level of a module.
* Fixed setting the ``related_name`` on a tags manager that exists on a model
  named ``Name``.

0.24.0 (2019-02-19)
~~~~~~~~~~~~~~~~~~~

* The project has moved to `Jazzband <https://jazzband.co/>`_. This is the
  first release under the new organization. The new repository URL is
  `<https://github.com/jazzband/django-taggit>`_.
* Added support for Django 2.2.
* Fixed a race condition in ``TaggableManager``.
* Removed method ``ItemBase.bulk_lookup_kwargs()``.
* Fixed view ``tagged_object_list`` to set ``queryset.model`` as
  ``ListView.model`` (was previously set as a ``ContentType`` instance).
* ``_TaggableManager`` and ``TaggableManager`` now always call the parent
  class ``__init__``.
* Removed ``TaggableRel`` and replaced uses with ``ManyToManyRel``.

0.23.0 (2018-08-07)
~~~~~~~~~~~~~~~~~~~

* **Backwards incompatible:** Remove support for Django < 1.11
* Added support for Django 2.1 and Python 3.7
* Moved TagWidget value conversion from TagWidget.render() to TagWidget.format_value()

0.22.2 (2017-12-27)
~~~~~~~~~~~~~~~~~~~

* Added support for Django 2.0
* **Backwards incompatible:** Dropped support for EOL Python 3.3

0.22.1 (2017-04-22)
~~~~~~~~~~~~~~~~~~~

* Update spanish translation
* Add testing for Django 1.11 and Python 3.6
* introduce isort and flake8 in the CI
* [docs] Fixed links to external apps
* Improved auto-slug in TagBase to support UUID pk
* [docs] Added contribution guidelines

0.22.0 (2017-01-29)
~~~~~~~~~~~~~~~~~~~

* **Backwards incompatible:** Drop support for Django 1.7

0.21.6 (2017-01-25)
~~~~~~~~~~~~~~~~~~~

* Fix case-insensitive tag creation when setting to a mix of new and existing
  tags are used

0.21.5 (2017-01-21)
~~~~~~~~~~~~~~~~~~~

* Check for case-insensitive duplicates when creating new tags

0.21.4 (2017-01-10)
~~~~~~~~~~~~~~~~~~~

* Support __gt__ and __lt__ ordering on Tags

0.21.3 (2016-10-07)
~~~~~~~~~~~~~~~~~~~

* Fix list view

0.21.2 (2016-08-31)
~~~~~~~~~~~~~~~~~~~

* Update Python version classifiers in setup.py
* Add Greek translation

0.21.1 (2016-08-25)
~~~~~~~~~~~~~~~~~~~

* Document supported versions of Django; fix Travis to test these versions.

0.21.0 (2016-08-22)
~~~~~~~~~~~~~~~~~~~

* Fix form tests on Django 1.10
* Address list_display and fieldsets in admin docs
* external_apps.txt improvements
* Remove support for Django 1.4-1.6, again.

0.20.2 (2016-07-11)
~~~~~~~~~~~~~~~~~~~

* Add extra_filters argument to the manager's most_common method

0.20.1 (2016-06-23)
~~~~~~~~~~~~~~~~~~~

* Specify `app_label` for `Tag` and `TaggedItem`

0.20.0 (2016-06-19)
~~~~~~~~~~~~~~~~~~~

* Fix UnboundLocalError in _TaggableManager.set(..)
* Update doc links to reflect RTD domain changes
* Improve Russian translations

0.19.1 (2016-05-25)
~~~~~~~~~~~~~~~~~~~

* Add app config, add simplified Chinese translation file

0.19.0 (2016-05-23)
~~~~~~~~~~~~~~~~~~~

* Implementation of m2m_changed signal sending
* Code and tooling improvements

0.18.3 (2016-05-12)
~~~~~~~~~~~~~~~~~~~

* Added Spanish and Turkish translations

0.18.2 (2016-05-08)
~~~~~~~~~~~~~~~~~~~

* Add the min_count parameter to managers.most_common function

0.18.1 (2016-03-30)
~~~~~~~~~~~~~~~~~~~

* Address deprecation warnings

0.18.0 (2016-01-18)
~~~~~~~~~~~~~~~~~~~

* Add option to override default tag string parsing
* Drop support for Python 2.6

0.17.6 (2015-12-09)
~~~~~~~~~~~~~~~~~~~

* Silence Django 1.9 warning

0.17.5 (2015-11-27)
~~~~~~~~~~~~~~~~~~~

* Django 1.9 compatibility fix

0.17.4 (2015-11-25)
~~~~~~~~~~~~~~~~~~~

* Allows custom Through Model with GenericForeignKey

0.17.3 (2015-10-26)
~~~~~~~~~~~~~~~~~~~

* Silence Django 1.9 warning about on_delete

0.17.2 (2015-10-25)
~~~~~~~~~~~~~~~~~~~

* Django 1.9 beta compatibility

0.17.1 (2015-09-10)
~~~~~~~~~~~~~~~~~~~

* Fix unknown column `object_id` issue with Django 1.6+

0.17.0 (2015-08-14)
~~~~~~~~~~~~~~~~~~~

* Database index added on TaggedItem fields content_type & object_id

0.16.4 (2015-08-13)
~~~~~~~~~~~~~~~~~~~

* Access default manager via class instead of instance

0.16.3 (2015-08-08)
~~~~~~~~~~~~~~~~~~~

* Prevent IntegrityError with custom TagBase classes

0.16.2 (2015-07-13)
~~~~~~~~~~~~~~~~~~~

* Fix an admin bug related to the `Manager` property `through_fields`

0.16.1 (2015-07-09)
~~~~~~~~~~~~~~~~~~~

* Fix bug that assumed all primary keys are named 'id'

0.16.0 (2015-07-04)
~~~~~~~~~~~~~~~~~~~

* Add option to allow case-insensitive tags

0.15.0 (2015-06-23)
~~~~~~~~~~~~~~~~~~~

* Fix wrong slugs for non-latin chars. Only works if optional GPL dependency
  (unidecode) is installed.

0.14.0 (2015-04-26)
~~~~~~~~~~~~~~~~~~~

* Prevent extra JOIN when prefetching
* Prevent _meta warnings with Django 1.8

0.13.0 (2015-04-02)
~~~~~~~~~~~~~~~~~~~

* Django 1.8 support

0.12.3 (2015-03-03)
~~~~~~~~~~~~~~~~~~~

* Specify that the internal type of the TaggitManager is a ManyToManyField

0.12.2 (2014-21-09)
~~~~~~~~~~~~~~~~~~~

* Fixed 1.7 migrations.

0.12.1 (2014-10-08)
~~~~~~~~~~~~~~~~~~~

* Final (hopefully) fixes for the upcoming Django 1.7 release.
* Added Japanese translation.

0.12.0 (2014-20-04)
~~~~~~~~~~~~~~~~~~~

* **Backwards incompatible:** Support for Django 1.7 migrations. South users
  have to set ``SOUTH_MIGRATION_MODULES`` to use ``taggit.south_migrations``
  for taggit.
* **Backwards incompatible:** Django's new transaction handling is used on
  Django 1.6 and newer.
* **Backwards incompatible:** ``Tag.save`` got changed to opportunistically try
  to save the tag and if that fails fall back to selecting existing similar
  tags and retry -- if that fails too an ``IntegrityError`` is raised by the
  database, your app will have to handle that.
* Added Italian and Esperanto translations.

0.11.2 (2013-13-12)
~~~~~~~~~~~~~~~~~~~

* Forbid multiple TaggableManagers via generic foreign keys.

0.11.1 (2013-25-11)
~~~~~~~~~~~~~~~~~~~

* Fixed support for Django 1.4 and 1.5.

0.11.0 (2013-25-11)
~~~~~~~~~~~~~~~~~~~

* Added support for prefetch_related on tags fields.
* Fixed support for Django 1.7.
* Made the tagging relations unserializeable again.
* Allow more than one TaggableManager on models (assuming concrete FKs are
   used for the relations).

0.10.0 (2013-17-08)
~~~~~~~~~~~~~~~~~~~

* Support for Django 1.6 and 1.7.
* Python3 support
* **Backwards incompatible:** Dropped support for Django < 1.4.5.
* Tag names are unique now, use the provided South migrations to upgrade.

0.9.2 (2011-01-17)
~~~~~~~~~~~~~~~~~~

* **Backwards incompatible:** Forms containing a :class:`TaggableManager` by
  default now require tags, to change this provide ``blank=True`` to the
  :class:`TaggableManager`.
* Now works with Django 1.3 (as of beta-1).

0.9.0 (2010-09-22)
~~~~~~~~~~~~~~~~~~

* Added a Hebrew locale.
* Added an index on the ``object_id`` field of ``TaggedItem``.
* When displaying tags always join them with commas, never spaces.
* The docs are now available `online <https://django-taggit.readthedocs.io/>`_.
* Custom ``Tag`` models are now allowed.
* **Backwards incompatible:** Filtering on tags is no longer
  ``filter(tags__in=["foo"])``, it is written
  ``filter(tags__name__in=["foo"])``.
* Added a German locale.
* Added a Dutch locale.
* Removed ``taggit.contrib.suggest``, it now lives in an external application,
   see :doc:`external_apps` for more information.

0.8.0 (2010-06-22)
~~~~~~~~~~~~~~~~~~

* Fixed querying for objects using ``exclude(tags__in=tags)``.
* Marked strings as translatable.
* Added a Russian translation.
* Created a `mailing list <http://groups.google.com/group/django-taggit>`_.
* Smarter tagstring parsing for form field; ported from Jonathan Buchanan's
  `django-tagging <http://django-tagging.googlecode.com>`_. Now supports tags
  containing commas. See :ref:`tags-in-forms` for details.
* Switched to using savepoints around the slug generation for tags. This
  ensures that it works fine on databases (such as Postgres) which dirty a
  transaction with an ``IntegrityError``.
* Added Python 2.4 compatibility.
* Added Django 1.1 compatibility.
