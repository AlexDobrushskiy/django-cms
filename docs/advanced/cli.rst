######################
Command Line Interface
######################

.. highlight:: bash

You can invoke the django CMS command line interface using the ``cms`` Django
command::

    python manage.py cms

**********************
Informational commands
**********************

.. _cms-list-command:

``cms list``
============

The ``list`` command is used to display information about your installation.

It has two subcommands:

* ``cms list plugins`` lists all plugins that are used in your project.
* ``cms list apphooks`` lists all apphooks that are used in your project.

``cms list plugins`` will issue warnings when it finds orphaned plugins (see
``cms delete_orphaned_plugins`` below).


.. _cms-check-command:

``cms check``
=============

Checks your configuration and environment.


**************************************
Plugin and apphook management commands
**************************************

.. _cms-delete-orphaned-plugins-command:

``cms delete_orphaned_plugins``
===============================

.. warning::

    The ``delete_orphaned_plugins`` command **permanently deletes** data from
    your database. You should make a backup of your database before using it!
    
Identifies and deletes orphaned plugins.

Orphaned plugins are ones that exist in the CMSPlugins table, but:

* have a plugin_type that is no longer even installed
* have no corresponding saved instance in that particular plugin type's table  

Such plugins will cause problems when trying to use operations that need to copy
pages (and thefore plugins), which includes ``cms moderator on`` as well as page
copy operations in the admin.

It is advised to run ``cms list plugins`` periodically, and ``cms
delete_orphaned_plugins`` when required.

``cms uninstall``
=================

The ``uninstall`` subcommand can be used to make uninstalling a CMS
Plugin or an apphook easier.

It has two subcommands:

* ``cms uninstall plugins <plugin name> [<plugin name 2> [...]]`` uninstalls
  one or several plugins by **removing** them from all pages where they are
  used. Note that the plugin name should be the name of the class that is
  registered in the django CMS. If you are unsure about the plugin name, use
  the :ref:`cms-list-command` to see a list of installed plugins.
* ``cms uninstall apphooks <apphook name> [<apphook name 2> [...]]`` uninstalls
  one or several apphooks by **removing** them from all pages where they are
  used. Note that the apphook name should be the name of the class that is
  registered in the django CMS. If you are unsure about the apphook name, use
  the :ref:`cms-list-command` to see a list of installed apphooks.

.. warning::

    The uninstall commands **permanently delete** data from your database.
    You should make a backup of your database before using them!

.. _cms-copy-lang-command:

``cms copy-lang``
=================

The ``copy-lang`` subcommand can be used to copy content (titles and plugins)
from one language to another.
By default the subcommand copy content from the current site
(e.g. the value of ``SITE_ID``) and only if the target
placeholder has no content for the specified language; using the defined
options you can change this.

You must provide two arguments:

* ``from_language``: the language to copy the content from;
* ``to_language``: the language to copy the content to.

It accepts the following options

* ``force-copy``: set to copy content even if a placeholder already has content;
  if set, copied content will be appended to the original one;
* ``site``: specifiy a SITE_ID to operate on sites different from the current one;
* ``verbose``: set for more verbose output.

Example::

    cms copy-lang en de force-copy site=2 verbose

*******************
Moderation commands
*******************

``cms moderator``
=================

If you migrate from an earlier version, you should use the ``cms moderator on``
command to ensure that your published pages are up to date, whether or not you
used moderation in the past.

.. warning::

    This command **alters data** in your database. You should make a backup of
    your database before using it! **Never** run this command without first 
    checking for orphaned plugins, using the ``cms list plugins`` command, and
    if necessary ``delete_orphaned_plugins``. Running  ``cms moderator`` with
    orphaned plugins will fail and leave bad data in your database.


*******************
MPTT repair command
*******************

``cms fix-mptt``
===================

Occasionally, the MPTT structure in which pages and plugins are held can
accumulate small errors. These are typically the result of failed operations or
large and complex restructurings of the tree (perhaps even cosmic rays,
planetary alignment or other mysterious conditions).

Usually you won't even notice them, and they won't affect the operation of the
system, but when you run into trouble it's useful to be able to rebuild the tree
- it's also useful to rebuild it as part of preventative maintenance.

.. warning::

    This command **alters data** in your database. You should make a backup of
    your database before using it!


**************************************
Additional commands
**************************************

``publisher_publish``
======================

If you want to publish many pages at once, this command can help you. By default,
this command publishes drafts for all public pages.

It accepts the following options

* ``unpublished``: set to publish all drafts, including unpublished ones;
  if not set, only already published pages will be republished.
* ``language``: specify a language code to publish pages in only one language;
  if not specified, this command publishes all page languages;
* ``site``: specify a site id to publish pages for specified site only;
  if not specified, this command publishes pages for all sites;


Example::

    #publish drafts for public pages in all languages
    publisher_publish

    #publish all drafts in all pages
    publisher_publish --unpublished

    #publish drafts for public pages in deutsch
    publisher_publish --language=de

    #publish all drafts in deutsch
    publisher_publish --unpublished --language=de

    #publish all drafts in deutsch, but only for site with id=2
    publisher_publish --unpublished --language=de --site=2

.. warning::

    This command publishes drafts. You should review drafts before using this
    command, because they will become public.
