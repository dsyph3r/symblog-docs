Creating a blog in Symfony2
===========================

Introduction
------------

This tutorial will guide you through the process of creating a full featured
blogging website using `Symfony2 <http://symfony.com/>`_. The Standard
Distribution of the Symfony2 framework will be used, which includes the main
components you will need when building your own websites. The tutorial is split
into a number of parts, each part covering different aspects of Symfony2 and its
components. This tutorial is aimed to be worked through similar to the
symfony 1 `Jobeet <http://www.symfony-project.org/jobeet/1_4/Doctrine/en/>`_
tutorial.

Tutorial Parts
~~~~~~~~~~~~~~

.. toctree::
    :maxdepth: 1

    docs/configuration-and-templating
    docs/validators-and-forms
    docs/doctrine-2-the-blog-model
    docs/extending-the-model-blog-comments
    docs/customising-the-view-more-with-twig
    docs/testing-unit-and-functional-phpunit

Demo Website
------------

The symblog website can be viewed at
`http://symblog.co.uk <http://symblog.co.uk/>`_. The source code is
available via `Github <https://github.com/dsyph3r/symblog>`_. It follows
along with each part of the tutorial

Coverage
--------

This tutorial aims to cover the common tasks you are faced with when creating
websites using Symfony2.

    1.  Bundles
    2.  Controllers
    3.  Templating (Using TWIG)
    4.  Model - Doctrine 2
    5.  Migrations
    6.  Data Fixtures
    7.  Validators
    8.  Forms
    9.  Routing
    10. Asset Management
    11. Emailing
    12. Environments
    13. Customising Error pages
    14. Security
    15. The User & Sessions
    16. CRUD Generation
    17. Caching
    18. Testing
    19. Deployment

Symfony2 is highly customisable and provides a number of different ways to
perform the same task. Some examples of this include writing configuration
options in YAML, XML, PHP, or Annotation, and creating templates using Twig or
PHP. To keep this tutorial simple we will use YAML and Annotations for
configuration and Twig for templating. The
`Symfony book <http://symfony.com/doc/current/book/index.html>`_
provides a great resource for examples of how to use the other methods.
If other people would like to contribute to the completion of alternative methods
simply fork the repository on `Github <https://github.com/dsyph3r/symblog-docs>`_
and send over the pull requests :)

Translations
------------

Spanish
~~~~~~~

Symblog has been translated into `Spanish <http://symblog.site90.net/>`_ thanks to the contribution by
`Lisper <https://twitter.com/#!/esymfony>`_.

French
~~~~~~~

Symblog has been translated into `French <http://keiruaprod.fr/symblog-fr/>`_ thanks to the contribution by
`Clement Keirua <https://twitter.com/clemkeirua>`_.

Author
------

This tutorial is being created by `dsyph3r <http://twitter.com/#!/dsyph3r>`_.

Contributing
------------

The `source <https://github.com/dsyph3r/symblog-docs>`_ for this tutorial is available on
Github. If you would like to improve and extend this tutorial simply fork the
project and send over the pull requests. You can also raise issues using the
`GitHub Issue Tracker <https://github.com/dsyph3r/symblog-docs/issues>`_. If any
one is interested in creating a much more visually pleasing design please get in
`touch <http://twitter.com/#!/dsyph3r>`_!

Credits
-------

Special thanks to all the contributors of the
`Official Symfony2 documentation <http://symfony.com/doc/current/>`_. This
provided an invaluable resource of information.

Flag Icons sourced from `famfamfam <http://www.famfamfam.com/lab/icons/flags/>`_.

Searching
---------

Looking for a specific topic? Use the :ref:`search`.
