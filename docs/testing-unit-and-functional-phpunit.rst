[Part 6] - Testing: Unit and Functional with PHPUnit
====================================================

Overview
--------

So far we have explored a good amount of ground looking at a number of core
concepts with regards to Symfony2 development. Before we continue adding
features it is time to introduce testing. We will look at how to test individual
functions with unit testing and how to ensure multiple components are working
correctly together with functional testing. The PHP testing library `PHPUnit
<http://www.phpunit.de/manual/current/en/>`_ will be covered as this library is
at the centre of the Symfony2 tests. As testing is a large topic it will also be
covered in later chapters. By the end of this chapter you will have written a
number of tests covering both unit and functional testing. You will have simulated
browser requests, populated forms with data, and checked responses to ensure
the website pages are outputting correctly. You will also have checked how much coverage
your tests have on your applications code base.

Testing in Symfony2
-------------------

`PHPUnit <http://www.phpunit.de/manual/current/en/>`_ has become the "de facto
standard" for writing tests in PHP, so learning it will benefit you in all your
PHP projects. Lets also not forget that most of the topics covered in this
chapter are language independent and so can be transferred to other languages
you. 

.. tip::

    If you are planning on writing your own Open Source Symfony2 bundles, 
    you are much more likely to receive interest if your bundle is well
    tested (and documented). Have a look at the existing Symfony2 bundles available
    at `Symfony2Bundles <http://symfony2bundles.org/>`_.

Unit Testing
~~~~~~~~~~~~

Unit testing is concerned with ensuring individual units of code function
correctly when used in isolation. In an Object Oriented code base like Symfony2,
a unit would be a class and its methods. For example, we could write tests for
the ``Blog`` and ``Comment`` Entity classes. When writing unit tests, the test
cases should be written independently of other test cases, i.e., the result of test
case B should not depend on the result of test case A. It is useful when unit testing
to be able to create mock objects that allow you to easily unit test
functions that have external dependencies. Mocking allows you to simulate a function
call instead of actually executing it. An example of this
would be unit testing a class that wraps up an external API. The API class may
use a transport layer for communicating with the external API. We could mock the
request method of the transport layer to return the results we specify, rather
than actually hitting the external API. Unit testing does not test that the
components of an application function correctly together, this is covered by the
next topic, functional testing.

Functional Testing
~~~~~~~~~~~~~~~~~~

Functional testing checks the integration of different components within the
application, such as routing, controllers, and views. Functional tests are
similar to the manual tests you would run yourself in the browser such as requesting
the homepage, clicking a blog link and checking the correct blog is shown.
Functional testing provides you with the ability to automate this process.
Symfony2 comes complete with a number of useful classes that assist in functional testing
including a ``Client`` that is able to requests pages and submit forms and DOM ``Crawler``
that we can use to traverse the ``Response`` from the client.

.. tip::

    There are a number of software development process that are driven by testing.
    These include processes such as Test Driven Development (TDD) and Behavioral
    Driven Development (BDD). While these are out side the scope of this tutorial
    you should be aware of the library written by `everzet
    <https://twitter.com/#!/everzet>`_ that facilitates BDD called `Behat
    <http://behat.org/>`_. There is also a Symfony2 `BehatBundle
    <http://docs.behat.org/bundle/index.html>`_ available to easily integrate Behat
    into your Symfony2 project.

PHPUnit
-------

As stated above, Symfony2 tests are written using PHPUnit. You will need to
install PHPUnit in order to run these tests and the tests from this chapter.
For detailed `installation instructions
<http://www.phpunit.de/manual/current/en/installation.html>`_ refer to the
official documentation on the PHPUnit website. To run the tests in Symfony2 you
need to install PHPUnit 3.5.11 or later. PHPUnit is a very large testing library, so references
to the official documentation will be made where additional reading can be
found. 

Assertions
~~~~~~~~~~

Writing tests is concerened with checking that the actual test result is equal
to the expected test result. There are a number of assertion methods available
in PHPUnit to assist you with this task. Some of the common assertion
methods you will use are listed below.

.. code-block:: php

    // Check 1 === 1 is true
    $this->assertTrue(1 === 1);

    // Check 1 === 2 is false
    $this->assertFalse(1 === 2);

    // Check 'Hello' equals 'Hello'
    $this->assertEquals('Hello', 'Hello');

    // Check array has key 'language'
    $this->assertArrayHasKey('language', array('language' => 'php', 'size' => '1024'));

    // Check array contains value 'php'
    $this->assertContains('php', array('php', 'ruby', 'c++', 'JavaScript'));

A full list of
`assertions <http://www.phpunit.de/manual/current/en/writing-tests-for-phpunit.html#writing-tests-for-phpunit.assertions>`_
is available in the PHPUnit documentation.

Running Symfony2 Tests
----------------------

Before we begin writing some tests, lets look at how we run tests in Symfony2. PHPUnit
can be set to execute using a configuration file. In our Symfony2 project this
file is located at ``app/phpunit.xml.dist``. As this file is suffixed with
``.dist``, you need to copy its contents into a file called ``app/phpunit.xml``.

.. tip::

    If you are using a VCS such as Git, you should add the new ``app/phpunit.xml``
    file to the VCS ignore list.

If you have a look at the contents of the PHPUnit configuration file you will see the
following.

.. code-block:: xml

    <!-- app/phpunit.xml -->
    
    <testsuites>
        <testsuite name="Project Test Suite">
            <directory>../src/*/*Bundle/Tests</directory>
            <directory>../src/*/Bundle/*Bundle/Tests</directory>
        </testsuite>
    </testsuites>

The following settings configure some directories that are part of our test suite.
When running PHPUnit it will look in the above directories for tests to run. You
can also pass additional command line arguments to PHPUnit to run tests in
specific directories, instead of the test suite tests. You will see how to
achieve this later.

You will also notice the configuration is specifying the bootstrap file located at
``app/bootstrap.php.cache``. This file is used by PHPUnit to get the testing environment
setup.

.. code-block:: xml

    <!-- app/phpunit.xml -->
    
    <phpunit
        bootstrap                   = "bootstrap.php.cache" >

.. tip::

    For more information regarding configuring PHPUnit with an XML file
    see the
    `PHPUnit documentation <http://www.phpunit.de/manual/current/en/organizing-tests.html#organizing-tests.xml-configuration>`_.

Running the Current Tests
-------------------------

As we used one of the Symfony2 generator tasks to create the
``BloggerBlogBundle`` back in chapter 1, it also created a controller test for
the ``DefaultController`` class. We can execute this test by running the
following command from the root directory of the project. The ``-c`` option
specifies that PHPUnit should load its configuration from the ``app`` directory.

.. code-block:: bash

    $ phpunit -c app

Once the testing has completed you should be notified that the tests failed.
If you look at the ``DefaultControllerTest`` class located at
``src/Blogger/BlogBundle/Tests/Controller/DefaultControllerTest.php`` you will see
the following content.

.. code-block:: php

    <?php
    // src/Blogger/BlogBundle/Tests/Controller/DefaultControllerTest.php

    namespace Blogger\BlogBundle\Tests\Controller;

    use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

    class DefaultControllerTest extends WebTestCase
    {
        public function testIndex()
        {
            $client = static::createClient();

            $crawler = $client->request('GET', '/hello/Fabien');

            $this->assertTrue($crawler->filter('html:contains("Hello Fabien")')->count() > 0);
        }
    }

This is a functional test for the ``DefaultController`` class that Symfony2 generated.
If you remember back to chapter 1, this Controller had an action that handled requests
to ``/hello/{name}``. The fact that we removed this controller is why the above test
is failing. Try going to the URL ``http://symblog.dev/app_dev.php/hello/Fabien`` in
your browser. You should be informed that the route could not be found. As the
above test makes a request to the same URL, it will also get the same response,
hence why the test fails. Functional testing is a large part of this chapter and
will be covered in detail later.

As the ``DefaultController`` class has been removed, you can also remove this test
class. Delete the ``DefaultControllerTest`` class located at
``src/Blogger/BlogBundle/Tests/Controller/DefaultControllerTest.php``.

Unit Testing
------------

As explained previously, unit testing is concerned with testing individual units
of your application in isolation. When writing unit tests it is recommend that you
replicate the Bundle structure under the Tests folder. For example, if you wanted
to test the ``Blog`` entity class located at
``src/Blogger/BlogBundle/Entity/Blog.php`` the test file would reside at
``src/Blogger/BlogBundle/Tests/Entity/BlogTest.php``. An example folder layout
would be as follows.

.. code-block:: text

    src/Blogger/BlogBundle/
                    Entity/
                        Blog.php
                        Comment.php
                    Controller/
                        PageController.php
                    Twig/
                        Extensions/
                            BloggerBlogExtension.php
                    Tests/
                        Entity/
                            BlogTest.php
                            CommentTest.php
                        Controller/
                            PageControllerTest.php
                        Twig/
                            Extensions/
                                BloggerBlogExtensionTest.php

Notice that each of the Test files are suffixed with Test.

Testing the Blog Entity - Slugify method
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We begin by testing the slugify method in the ``Blog`` entity. Lets write some
tests to ensure this method is working correctly. Create a new file located at
``src/Blogger/BlogBundle/Tests/Entity/BlogTest.php`` and add the following.

.. code-block:: php

    <?php
    // src/Blogger/BlogBundle/Tests/Entity/BlogTest.php

    namespace Blogger\BlogBundle\Tests\Entity;

    use Blogger\BlogBundle\Entity\Blog;

    class BlogTest extends \PHPUnit_Framework_TestCase
    {

    }

We have created a test class for the ``Blog`` entity. Notice the location of the file
complies with the folder structure mentioned above. The ``BlogTest`` class extends
the base PHPUnit class ``PHPUnit_Framework_TestCase``. All tests you write for PHPUnit
will be a child of this class. You'll remember from previous chapters that
the ``\`` must be placed in front of the ``PHPUnit_Framework_TestCase`` class
name as the class is declared in the PHP public namespace.

Now we have the skeleton class for our ``Blog`` entity tests, lets write a test
case. Test cases in PHPUnit are methods of the Test class prefixed
with ``test``, such as ``testSlugify()``. Update the ``BlogTest`` located at
``src/Blogger/BlogBundle/Tests/Entity/BlogTest.php`` with the following.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Entity/BlogTest.php

    // ..

    class BlogTest extends \PHPUnit_Framework_TestCase
    {
        public function testSlugify()
        {
            $blog = new Blog();

            $this->assertEquals('hello-world', $blog->slugify('Hello World'));
        }
    }

This is a very simple test case. It instantiates a new ``Blog`` entity and runs
an ``assertEquals()`` on the result of the ``slugify`` method. The ``assertEquals()``
method takes 2 mandatory arguments, the expected result and the actual result.
An optional 3rd argument can be passed in to specify a message to display
when the test case fails.

Lets run our new unit test. Run the following on the command line.

.. code-block:: bash

    $ phpunit -c app

You should see the following output.

.. code-block :: bash

    PHPUnit 3.5.11 by Sebastian Bergmann.

    .

    Time: 1 second, Memory: 4.25Mb

    OK (1 test, 1 assertion)

The output from PHPUnit is very simple, Its start by displaying some information about
PHPUnit and the outputs a number of ``.`` for each test it runs, in our case
we are only running 1 test so only 1 ``.`` is output. The last statement informs
us of the result of the tests. For our ``BlogTest`` we only ran 1 test with 1
assertion. If you have color output on your command line you will also see the
last line displayed in green showing everything executed OK.
Lets update the ``testSlugify()`` method to see what happens when the tests fails.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Entity/BlogTest.php

    // ..

    public function testSlugify()
    {
        $blog = new Blog();

        $this->assertEquals('hello-world', $blog->slugify('Hello World'));
        $this->assertEquals('a day with symfony2', $blog->slugify('A Day With Symfony2'));
    }

Re run the unit tests as before. The following output will be displayed

.. code-block :: bash

    PHPUnit 3.5.11 by Sebastian Bergmann.

    F

    Time: 0 seconds, Memory: 4.25Mb

    There was 1 failure:

    1) Blogger\BlogBundle\Tests\Entity\BlogTest::testSlugify
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -a day with symfony2
    +a-day-with-symfony2

    /var/www/html/symblog/symblog/src/Blogger/BlogBundle/Tests/Entity/BlogTest.php:15

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

The output is a bit more involved this time. We can see the ``.`` for the run tests
is replaced by a ``F``. This tells us the test failed. You will also see the ``E``
character output if your test contains Errors. Next PHPUnit notifies us in
detail of the failures, in this case, the 1 failure. We can see the
``Blogger\BlogBundle\Tests\Entity\BlogTest::testSlugify`` method failed because
the Expected and the Actual values were different. If you have color output on
your command line you will also see the last line displayed in red showing
there were failures in your tests. Correct the ``testSlugify()`` method so
the tests execute successfully.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Entity/BlogTest.php

    // ..

    public function testSlugify()
    {
        $blog = new Blog();

        $this->assertEquals('hello-world', $blog->slugify('Hello World'));
        $this->assertEquals('a-day-with-symfony2', $blog->slugify('A Day With Symfony2'));
    }

Before moving on add some more test for ``slugify()`` method.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Entity/BlogTest.php

    // ..

    public function testSlugify()
    {
        $blog = new Blog();

        $this->assertEquals('hello-world', $blog->slugify('Hello World'));
        $this->assertEquals('a-day-with-symfony2', $blog->slugify('A Day With Symfony2'));
        $this->assertEquals('hello-world', $blog->slugify('Hello    world'));
        $this->assertEquals('symblog', $blog->slugify('symblog '));
        $this->assertEquals('symblog', $blog->slugify(' symblog'));
    }

Now we have tested the ``Blog`` entity slugify method, we need to  ensure the ``Blog``
``$slug`` member is correctly set when the ``$title`` member of the ``Blog`` is
updated. Add the following methods to the ``BlogTest`` file located at
``src/Blogger/BlogBundle/Tests/Entity/BlogTest.php``.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Entity/BlogTest.php

    // ..

    public function testSetSlug()
    {
        $blog = new Blog();

        $blog->setSlug('Symfony2 Blog');
        $this->assertEquals('symfony2-blog', $blog->getSlug());
    }

    public function testSetTitle()
    {
        $blog = new Blog();

        $blog->setTitle('Hello World');
        $this->assertEquals('hello-world', $blog->getSlug());
    }

We begin by testing the ``setSlug`` method to ensure the ``$slug`` member is
correctly slugified when updated. Next we check the ``$slug`` member is correctly
updated when the ``setTitle`` method is called on the ``Blog`` entity.

Run the tests to verify the ``Blog`` entity is functioning correctly.

Testing the Twig extension
~~~~~~~~~~~~~~~~~~~~~~~~~~

In the previous chapter we created a Twig extension to convert a ``\DateTime``
instance into a string detailing the duration since a time period. Create a new test file located at
``src/Blogger/BlogBundle/Tests/Twig/Extensions/BloggerBlogExtensionTest.php`` and
update with the following content.

.. code-block:: php

    <?php
    // src/Blogger/BlogBundle/Tests/Twig/Extensions/BloggerBlogExtensionTest.php

    namespace Blogger\BlogBundle\Tests\Twig\Extensions;

    use Blogger\BlogBundle\Twig\Extensions\BloggerBlogExtension;

    class BloggerBlogExtensionTest extends \PHPUnit_Framework_TestCase
    {
        public function testCreatedAgo()
        {
            $blog = new BloggerBlogExtension();

            $this->assertEquals("0 seconds ago", $blog->createdAgo(new \DateTime()));
            $this->assertEquals("34 seconds ago", $blog->createdAgo($this->getDateTime(-34)));
            $this->assertEquals("1 minute ago", $blog->createdAgo($this->getDateTime(-60)));
            $this->assertEquals("2 minutes ago", $blog->createdAgo($this->getDateTime(-120)));
            $this->assertEquals("1 hour ago", $blog->createdAgo($this->getDateTime(-3600)));
            $this->assertEquals("1 hour ago", $blog->createdAgo($this->getDateTime(-3601)));
            $this->assertEquals("2 hours ago", $blog->createdAgo($this->getDateTime(-7200)));

            // Cannot create time in the future
            $this->setExpectedException('\InvalidArgumentException');
            $blog->createdAgo($this->getDateTime(60));
        }

        protected function getDateTime($delta)
        {
            return new \DateTime(date("Y-m-d H:i:s", time()+$delta));
        }
    }

The class is setup much the same as before, creating a method ``testCreatedAgo()``
to test the Twig Extension. We introduce another PHPUnit method in this test case,
the ``setExpectedException()`` method. This method should be called before executing
a method you expect to throw an exception. We know that the ``createdAgo`` method
of the Twig extension cannot handle dates in the future and will throw an
``\Exception``. The ``getDateTime()`` method is simply a helper method for
creating a ``\DateTime`` instance. Notice it is not prefixed with ``test`` so
PHPUnit will not try to execute it as a test case. Open up the command line
and run the tests for this file. We could simply run the test as before, but
we can also tell PHPUnit to run tests for a specific folder (and its sub folders)
or a file. Run the following command.

.. code-block:: bash

    $ phpunit -c app src/Blogger/BlogBundle/Tests/Twig/Extensions/BloggerBlogExtensionTest.php

This will run the tests for the ``BloggerBlogExtensionTest`` file only. PHPUnit
will inform us that the tests failed. The output is shown below.

.. code-block:: bash

    1) Blogger\BlogBundle\Tests\Twig\Extension\BloggerBlogExtensionTest::testCreatedAgo
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -0 seconds ago
    +0 second ago

    /var/www/html/symblog/symblog/src/Blogger/BlogBundle/Tests/Twig/Extensions/BloggerBlogExtensionTest.php:14

We were expecting the first assertion to return ``0 seconds ago`` but it didn't, the
word second was not plural. Lets update the Twig Extension located at
``src/Blogger/BlogBundle/Twig/Extensions/BloggerBlogBundle.php`` to correct this.

.. code-block:: php

    <?php
    // src/Blogger/BlogBundle/Twig/Extensions/BloggerBlogBundle.php

    namespace Blogger\BlogBundle\Twig\Extensions;

    class BloggerBlogExtension extends \Twig_Extension
    {
        // ..

        public function createdAgo(\DateTime $dateTime)
        {
            // ..
            if ($delta < 60)
            {
                // Seconds
                $time = $delta;
                $duration = $time . " second" . (($time === 0 || $time > 1) ? "s" : "") . " ago";
            }
            // ..
        }

        // ..
    }

Re run the PHPUnit tests. You should see the first assertion passing correctly,
but our test case still fails. Lets examine the next output.

.. code-block:: bash

    1) Blogger\BlogBundle\Tests\Twig\Extension\BloggerBlogExtensionTest::testCreatedAgo
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -1 hour ago
    +60 minutes ago

    /var/www/html/symblog/symblog/src/Blogger/BlogBundle/Tests/Twig/Extensions/BloggerBlogExtensionTest.php:18

We can see now that the 5th assertion is failing (notice the 18 at the end of the
output, this gives us the line number in the file where the assertion failed).
Looking at the test case we can see that the Twig Extension has functioned
incorrectly. 1 hour ago should have been returned, but instead 60 minutes ago
was. If we examine the code in the ``BloggerBlogExtension`` Twig
extension we can see the reason. We compare the time to be inclusive, i.e., we use
``<=`` rather than ``<``. We can also see this is the case when checking for
hours. Update the Twig extension located at
``src/Blogger/BlogBundle/Twig/Extensions/BloggerBlogBundle.php`` to correct
this.

.. code-block:: php

    <?php
    // src/Blogger/BlogBundle/Twig/Extensions/BloggerBlogBundle.php

    namespace Blogger\BlogBundle\Twig\Extensions;

    class BloggerBlogExtension extends \Twig_Extension
    {
        // ..

        public function createdAgo(\DateTime $dateTime)
        {
            // ..

            else if ($delta < 3600)
            {
                // Mins
                $time = floor($delta / 60);
                $duration = $time . " minute" . (($time > 1) ? "s" : "") . " ago";
            }
            else if ($delta < 86400)
            {
                // Hours
                $time = floor($delta / 3600);
                $duration = $time . " hour" . (($time > 1) ? "s" : "") . " ago";
            }

            // ..
        }

        // ..
    }

Now re run all our tests using the following command.

.. code-block:: bash

    $ phpunit -c app

This runs all our tests, and shows all tests pass successfully. Although we have
only written a small number of unit tests you should be getting a feel for how
powerful and important testing is when writing code. While the above errors
were minor, they were still errors. Testing also helps any future functionality
added to the project breaking previous features. This concludes the unit testing
for now. We will see more unit testing in the following chapters. Try adding some
of your own unit tests to test functionality that has been missed.

Functional Testing
------------------

Now we have written some unit tests, lets move on to testing multiple components
together. The first section of the functional testing will involve simulating
browser requests to tests the generated responses.

Testing the About page
~~~~~~~~~~~~~~~~~~~~~~

We begin testing the ``PageController`` class for the about page. As the about page
is very simple, this is a good place to start. Create a new file located at
``src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php`` and add
the following content.

.. code-block:: php

    <?php
    // src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

    namespace Blogger\BlogBundle\Tests\Controller;

    use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

    class PageControllerTest extends WebTestCase
    {
        public function testAbout()
        {
            $client = static::createClient();

            $crawler = $client->request('GET', '/about');

            $this->assertEquals(1, $crawler->filter('h1:contains("About symblog")')->count());
        }
    }

We have already seen a Controller test very similar to this when we briefly looked
at the ``DefaultControllerTest`` class. This is testing the about page of symblog,
checking the string ``About symblog`` is present in the generated HTML,
specifically within the ``H1`` tag. The ``PageControllerTest`` class doesn't extend the
``\PHPUnit_Framework_TestCase`` as we saw with the unit testing examples,
it instead extends the class ``WebTestCase``. This class is part of the Symfony2
FrameworkBundle.


As explained before PHPUnit test classes must extend the
``\PHPUnit_Framework_TestCase``, but when extra or common functionality is
required across multiple Test cases it is useful to encapsulate this in its
own class and have your Test classes extend this. The ``WebTestCase`` does
exactly this, it provides a number of useful method for running functional tests
in Symfony2. Have a look at the ``WebTestCase`` file located at
``vendor/symfony/src/Symfony/Bundle/FrameworkBundle/Test/WebTestCase.php``, you
will see that this class is in fact extending the ``\PHPUnit_Framework_TestCase``
class.

.. code-block:: php

    // vendor/symfony/src/Symfony/Bundle/FrameworkBundle/Test/WebTestCase.php

    abstract class WebTestCase extends \PHPUnit_Framework_TestCase
    {
        // ..
    }

If you look at the ``createClient()`` method in the ``WebTestCase`` class
you can see it creates an instance of the Symfony2 Kernel. Following the methods
through you will also notice that the ``environment`` is set to ``test``
(unless overridden as one of the arguments to ``createClient()``). This is the
``test`` environment we spoke about in the previous chapter.

Looking back at our test class we can see the ``createClient()`` method is
called to get the test up and running. We then call the ``request()`` method on the
client to simulate a browser HTTP GET request to the url ``/about`` (this would
be just like you visiting ``http://symblog.dev/about`` in your browser). The
request gives us a ``Crawler`` object back, which contains the ``Response``. The
``Crawler`` class is very useful as it lets us traverse the returned HTML. We
use the ``Crawler`` instance to check that the ``H1`` tag in the response HTML
contains the words ``About symblog``. You'll notice that even though we are
extending the class ``WebTestCase`` we still use the assert method as before
(remember the ``PageControllerTest`` class is still is child of the
``\PHPUnit_Framework_TestCase`` class).

Lets run the ``PageControllerTest`` using the following command. When writing
tests its useful to only execute the tests for the file you are currently working on.
As your test suite gets large, running tests can be a time consuming tasks.

.. code-block:: bash

    $ phpunit -c app/ src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

You should be greeted with the message ``OK (1 test, 1 assertion)`` letting us
know that 1 test (the ``testAbout()``) ran, with 1 assertion (the ``assertEquals()``).

Try changing the ``About symblog`` string to ``Contact`` and then re run the test.
The test will now fail as ``Contact`` wont be found, causing ``asertEquals`` to
equate to false.

.. code-block:: bash

    1) Blogger\BlogBundle\Tests\Controller\PageControllerTest::testAbout
    Failed asserting that 0 matches expected 1.

Revert the string back to ``About symblog`` before moving on.

The ``Crawler`` instance used allows you to traverse either HTML or XML
documents (which means the ``Crawler`` will only work with responses that return
HTML or XML). We can use the ``Crawler`` to traverse the generated response using
methods such as ``filter()``, ``first()``, ``last()``, and ``parents()``. If you
have used `jQuery <http://jquery.com/>`_ before you should feel right at home
with the ``Crawler`` class. A full list of supported ``Crawler`` traversal methods can be
found in the `Testing
<http://symfony.com/doc/current/book/testing.html#traversing>`_ chapter of the
Symfony2 book. We will explore more of the ``Crawler`` features as we continue.

Homepage
~~~~~~~~

While the test for the about page was simple, it has outlined the basic principles
of functional testing the website pages.

 1. Create the client
 2. Request a page
 3. Check the response

This is a simple overview of the process, in fact there are a number of other
steps we could also do such as clicking links and populating and submitting
forms.

Lets create a method to test the homepage. We know the homepage is available
via the URL ``/`` and that is should display the latest blog posts. Add a new
method ``testIndex()`` to the ``PageControllerTest`` class located at
``src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php`` as shown below.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

    public function testIndex()
    {
        $client = static::createClient();

        $crawler = $client->request('GET', '/');

        // Check there are some blog entries on the page
        $this->assertTrue($crawler->filter('article.blog')->count() > 0);
    }

You can see the same steps are taken as with the tests for the about page.
Run the test to ensure everything is working as expected.

.. code-block:: bash

    $ phpunit -c app/ src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

Lets now take the testing a bit further. Part of functional testing involves being
able to replicate what a user would do on the site. In order for users to move
between pages on your website they click links. Lets simulate this action now
to test the links to the show blog page work correctly when the blog title is clicked.
Update the ``testIndex()`` method in the ``PageControllerTest`` class with the following.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

    public function testIndex()
    {
        // ..

        // Find the first link, get the title, ensure this is loaded on the next page
        $blogLink   = $crawler->filter('article.blog h2 a')->first();
        $blogTitle  = $blogLink->text();
        $crawler    = $client->click($blogLink->link());

        // Check the h2 has the blog title in it
        $this->assertEquals(1, $crawler->filter('h2:contains("' . $blogTitle .'")')->count());
    }

The first thing we do it use the ``Crawler`` to extract the text within the first
blog title link. This is done using the filter ``article.blog h2 a``. This filter
is used return the ``a`` tag within the ``H2`` tag of the ``article.blog``
article. To understand this better, have a look at the markup used on the homepage
for displaying blogs.

.. code-block:: html

    <article class="blog">
        <div class="date"><time datetime="2011-09-05T21:06:19+01:00">Monday, September 5, 2011</time></div>
        <header>
            <h2><a href="/app_dev.php/1/a-day-with-symfony2">A day with Symfony2</a></h2>
        </header>

        <!-- .. -->
    </article>
    <article class="blog">
        <div class="date"><time datetime="2011-09-05T21:06:19+01:00">Monday, September 5, 2011</time></div>
        <header>
            <h2><a href="/app_dev.php/2/the-pool-on-the-roof-must-have-a-leak">The pool on the roof must have a leak</a></h2>
        </header>

        <!-- .. -->
    </article>

You can see the filter ``article.blog h2 a`` structure in place in the homepage
markup. You'll also notice that there is more than one ``<article class="blog">`` in
the markup, meaning the ``Crawler`` filter will return a collection. As we only want
the first link, we use the ``first()`` method on the collection. Finally we use
the ``text()`` method to extract the link text, in this case it will be the text
``A day with Symfony2``. Next, the blog title link is clicked to navigate to the
blog show page. The client ``click()`` method takes a link object and returns the
``Response`` in a ``Crawler`` instance. You should by now be noticing that the
``Crawler`` object is a key part to functional testing.

The ``Crawler`` object now contains the Response for the blog show page. We need
to test that the link we navigated took us to the right page. We can use the
``$blogTitle`` value we retrieved earlier to check this against the title in the
Response.

Run the tests to ensure that navigation between the homepage and the blog show
pages is working correctly.

.. code-block:: bash

    $ phpunit -c app/ src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

Now you have an understanding of how to navigate through the website pages
when functional testing, lets move onto testing forms.

Testing the Contact Page
~~~~~~~~~~~~~~~~~~~~~~~~

Users of symblog are able to submit contact enquiries by completing the form on
the contact page ``http://symblog.dev/contact``. Lets test that submissions
of this form work correctly. First we need to outline what should happen when
the form is successfully submitted (successfully submitted in this case means
there are no errors present in the form).

 1. Navigate to contact page
 2. Populate contact form with values
 3. Submit form
 4. Check email was sent to symblog
 5. Check response to client contains notification of successful contact

So far we have explored enough to be able to complete steps 1 and 5 only. We will
now look at how to test the 3 middle steps.

Add a new method ``testContact()`` to the ``PageControllerTest`` class located at
``src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php``.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

    public function testContact()
    {
        $client = static::createClient();

        $crawler = $client->request('GET', '/contact');

        $this->assertEquals(1, $crawler->filter('h1:contains("Contact symblog")')->count());

        // Select based on button value, or id or name for buttons
        $form = $crawler->selectButton('Submit')->form();

        $form['blogger_blogbundle_enquirytype[name]']       = 'name';
        $form['blogger_blogbundle_enquirytype[email]']      = 'email@email.com';
        $form['blogger_blogbundle_enquirytype[subject]']    = 'Subject';
        $form['blogger_blogbundle_enquirytype[body]']       = 'The comment body must be at least 50 characters long as there is a validation constrain on the Enquiry entity';

        $crawler = $client->submit($form);

        $this->assertEquals(1, $crawler->filter('.blogger-notice:contains("Your contact enquiry was successfully sent. Thank you!")')->count());
    }

We begin in the usual fashion, making a request to the ``/contact`` URL, and
checking the page contains the correct ``H1`` title. Next we use the ``Crawler``
to select the form submit button. The reason we select the button and not the
form is that a form may contain multiple buttons that we may want to click
independently. From the selected button we are able to retrieve the form. We are
able to set the form values using the array subscript notation ``[]``.
Finally the form is passed to the client ``submit()`` method to actually
submit the form. As usual, we receive a ``Crawler`` instance back. Using the
``Crawler`` response we check to ensure the flash message is present in the returned
response. Run the test to check everything is functioning correctly.

.. code-block:: bash

    $ phpunit -c app/ src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

The tests failed. We are given the following output from PHPUnit.

.. code-block:: bash

    1) Blogger\BlogBundle\Tests\Controller\PageControllerTest::testContact
    Failed asserting that <integer:0> matches expected <integer:1>.

    /var/www/html/symblog/symblog/src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php:53

    FAILURES!
    Tests: 3, Assertions: 5, Failures: 1.

The output is informing us that the flash message could not be found in the
response from the form submit. This is because when in the ``test`` environment,
redirects are not followed. When the form is successfully validated in the
``PageController`` class a redirect happens. This redirect is not being
followed; We need to explicitly say that the redirect should be followed. The
reason redirects are not followed is simple, you may want to check the current
response first. We will demonstrate this soon to check the email was sent.
Update the ``PageControllerTest`` class to set the client to follow the
redirect.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

    public function testContact()
    {
        // ..

        $crawler = $client->submit($form);

        // Need to follow redirect
        $crawler = $client->followRedirect();

        $this->assertEquals(1, $crawler->filter('.blogger-notice:contains("Your contact enquiry was successfully sent. Thank you!")')->count());
    }

No when you run the PHPUnit tests they should pass. Lets now look at the final
step of checking the contact form submission process, step 4, checking an email
was sent to symblog. We already know that emails will not be delivered in the
``test`` environment due to the following configuration.

.. code-block:: yaml

    # app/config/config_test.yml

    swiftmailer:
        disable_delivery: true

We can test the emails were sent using the information gathered by the web profiler.
This is where the importance of the client not following redirects comes in. The
check on the profiler needs to be done before the redirect happens, as the information
in the profiler will be lost. Update the ``testContact()`` method with the following.

.. code-block:: php

    // src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

    public function testContact()
    {
        // ..

        $crawler = $client->submit($form);

        // Check email has been sent
        if ($profile = $client->getProfile())
        {
            $swiftMailerProfiler = $profile->getCollector('swiftmailer');

            // Only 1 message should have been sent
            $this->assertEquals(1, $swiftMailerProfiler->getMessageCount());

            // Get the first message
            $messages = $swiftMailerProfiler->getMessages();
            $message  = array_shift($messages);

            $symblogEmail = $client->getContainer()->getParameter('blogger_blog.emails.contact_email');
            // Check message is being sent to correct address
            $this->assertArrayHasKey($symblogEmail, $message->getTo());
        }

        // Need to follow redirect
        $crawler = $client->followRedirect();

        $this->assertTrue($crawler->filter('.blogger-notice:contains("Your contact enquiry was successfully sent. Thank you!")')->count() > 0);
    }

After the form submit we check to see if the profiler is available as it may have
been disabled by a configuration setting for the current environment.

.. tip::

    Remember tests don't have to be run in the ``test`` environment, they could be
    run on the ``production`` environment where things like the profiler wont be
    available.

If we are able to get the profiler we make a request to retrieve the
``swiftmailer`` collector. The ``swiftmailer`` collector works behind the scenes
to gather information about how the emailing service is used. We can use this to
get information regarding which emails have been sent.

Next we use the ``getMessageCount()`` method to check that 1 email was sent. This
maybe enough to ensure that at least an email is going to be sent, but it doesn't verify
that the email will be sent to the correct location. It could be very embarrassing
or even damaging for emails to be sent to the wrong email address. To check this
isn't the case we verify the email to address is correct.

Now re run the tests to check everything is working correctly.

.. code-block:: bash

    $ phpunit -c app/ src/Blogger/BlogBundle/Tests/Controller/PageControllerTest.php

Testing Adding Blog Comments
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Lets now use the knowledge we have gained from the previous tests on the contact page
to test the process of submitting a blog comment.
Again we outline what should happen when the form is successfully
submitted.

 1. Navigate to a blog page
 2. Populate comment form with values
 3. Submit form
 4. Check new comment is added to end of blog comment list
 5. Also check sidebar latest comments to ensure comment is at top of list

Create a new file located at
``src/Blogger/BlogBundle/Tests/Controller/BlogControllerTest.php``
and add in the following.

.. code-block:: php

    <?php
    // src/Blogger/BlogBundle/Tests/Controller/BlogControllerTest.php

    namespace Blogger\BlogBundle\Tests\Controller;

    use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

    class BlogControllerTest extends WebTestCase
    {
        public function testAddBlogComment()
        {
            $client = static::createClient();

            $crawler = $client->request('GET', '/1/a-day-with-symfony');

            $this->assertEquals(1, $crawler->filter('h2:contains("A day with Symfony2")')->count());

            // Select based on button value, or id or name for buttons
            $form = $crawler->selectButton('Submit')->form();

            $crawler = $client->submit($form, array(
                'blogger_blogbundle_commenttype[user]'          => 'name',
                'blogger_blogbundle_commenttype[comment]'       => 'comment',
            ));

            // Need to follow redirect
            $crawler = $client->followRedirect();

            // Check comment is now displaying on page, as the last entry. This ensure comments
            // are posted in order of oldest to newest
            $articleCrawler = $crawler->filter('section .previous-comments article')->last();

            $this->assertEquals('name', $articleCrawler->filter('header span.highlight')->text());
            $this->assertEquals('comment', $articleCrawler->filter('p')->last()->text());

            // Check the sidebar to ensure latest comments are display and there is 10 of them

            $this->assertEquals(10, $crawler->filter('aside.sidebar section')->last()
                                            ->filter('article')->count()
            );

            $this->assertEquals('name', $crawler->filter('aside.sidebar section')->last()
                                                ->filter('article')->first()
                                                ->filter('header span.highlight')->text()
            );
        }
    }

We jump straight in this time with the entire test. Before we begin dissecting the code,
run the tests for this file to ensure everything is working correctly.

.. code-block:: bash

    $ phpunit -c app/ src/Blogger/BlogBundle/Tests/Controller/BlogControllerTest.php

PHPUnit should inform you that the 1 test was executed successfully. Looking at
the code for the ``testAddBlogComment()`` we can see things begin in the usual
format, creating a client, requesting a page and checking the page we are on is
correct. We then proceed to get the add comment form, and submit the form. The
way we populate the form values is slightly different than the previous version.
This time we use the 2nd argument of the client ``submit()`` method to pass in
the values for the form.

.. tip::

    We could also use the Object Oriented interface to set the values of the form fields.
    Some examples are shown below.

    .. code-block:: php

        // Tick a checkbox
        $form['show_emal']->tick();
        
        // Select an option or a radio
        $form['gender']->select('Male');

After submitting the form, we request the client should follow the redirect so we
can check the response. We use the ``Crawler`` again to get the last blog comment, which
should be the one we just submitted. Finally we check the latest comments in the
sidebar to check the comment is also the first one in the list.

Blog Repository
~~~~~~~~~~~~~~~

The last part of the functional testing we will explore in this chapter is
testing a Doctrine 2 repository. Create a new file located at
``src/Blogger/BlogBundle/Tests/Repository/BlogRepositoryTest.php`` and add the
following content.

.. code-block:: php

    <?php
    // src/Blogger/BlogBundle/Tests/Repository/BlogRepositoryTest.php

    namespace Blogger\BlogBundle\Tests\Repository;

    use Blogger\BlogBundle\Repository\BlogRepository;
    use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

    class BlogRepositoryTest extends WebTestCase
    {
        /**
         * @var \Blogger\BlogBundle\Repository\BlogRepository
         */
        private $blogRepository;

        public function setUp()
        {
            $kernel = static::createKernel();
            $kernel->boot();
            $this->blogRepository = $kernel->getContainer()
                                           ->get('doctrine.orm.entity_manager')
                                           ->getRepository('BloggerBlogBundle:Blog');
        }

        public function testGetTags()
        {
            $tags = $this->blogRepository->getTags();

            $this->assertTrue(count($tags) > 1);
            $this->assertContains('symblog', $tags);
        }

        public function testGetTagWeights()
        {
            $tagsWeight = $this->blogRepository->getTagWeights(
                array('php', 'code', 'code', 'symblog', 'blog')
            );

            $this->assertTrue(count($tagsWeight) > 1);

            // Test case where count is over max weight of 5
            $tagsWeight = $this->blogRepository->getTagWeights(
                array_fill(0, 10, 'php')
            );

            $this->assertTrue(count($tagsWeight) >= 1);

            // Test case with multiple counts over max weight of 5
            $tagsWeight = $this->blogRepository->getTagWeights(
                array_merge(array_fill(0, 10, 'php'), array_fill(0, 2, 'html'), array_fill(0, 6, 'js'))
            );

            $this->assertEquals(5, $tagsWeight['php']);
            $this->assertEquals(3, $tagsWeight['js']);
            $this->assertEquals(1, $tagsWeight['html']);

            // Test empty case
            $tagsWeight = $this->blogRepository->getTagWeights(array());

            $this->assertEmpty($tagsWeight);
        }
    }

As we want to perform tests that require a valid connection to the database
we extend the ``WebTestCase`` again as this allows us to bootstrap the Symfony2
Kernel. Run this test for this file using the following command.

.. code-block:: bash

    $ phpunit -c app/ src/Blogger/BlogBundle/Tests/Repository/BlogRepositoryTest.php

Code Coverage
-------------

Before we move on lets quickly touch on code coverage. Code coverage gives us an
insight into which parts of the code are executed when the tests are run. Using
this we can see the parts of our code that have no tests run on them, and
determine if we need to write test for them.

To output the code coverage analysis for your application run the following

.. code-block:: bash

    $ phpunit --coverage-html ./phpunit-report -c app/

This will output the code coverage analysis to the folder ``phpunit-report``.
Open the ``index.html`` file in your browser to see the analysis output.

See the `Code Coverage Analysis <http://www.phpunit.de/manual/current/en/code-coverage-analysis.html>`_
chapter in the PHPUnit documentation for more information.

Conclusion
----------

We have covered a number of key areas with regards to testing. We have explored
both unit and functional testing to ensure our website is functioning correctly.
We have seen how to simulate browser requests and how to use the Symfony2 ``Crawler``
class to check the Response from these requests.

Next we will look at the Symfony2 security component, and more specifically how to use it
for user management. We will also integrate the FOSUserBundle ready for us to work on the
symblog admin section.
