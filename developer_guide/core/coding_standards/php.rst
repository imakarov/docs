***
PHP
***

================================
The Right Way of PHP Development
================================

Before you start working, read `PHP The Right Way <http://www.phptherightway.com/>`_.

The Boy Scouts have a rule: "Always leave the campground cleaner than you found it." If you find a mess on the ground, you clean it up regardless of who might have made the mess. <...> (by `Uncle Bob <http://programmer.97things.oreilly.com/wiki/index.php/Uncle_Bob>`_ at `O’Reilly Commons <http://programmer.97things.oreilly.com/wiki/index.php/The_Boy_Scout_Rule>`_)

Please rewrite the code that doesn’t follow the rules and standards outlined here, even if the code isn’t yours.

=========
Standards
=========

-------------
General Style
-------------

1. The code must fully comply with `PSR-1 <http://www.php-fig.org/psr/psr-1/>`_ and `PSR-2 <http://www.php-fig.org/psr/psr-2/>`_.

2. Use `PHP Coding Standards Fixer <http://cs.sensiolabs.org/>`_ to check your code for compliance with those standards.

---------------------------------------------------
Names of Variables, Array Keys and Class Properties
---------------------------------------------------

1. The names of variables **must** be written in lowercase, using the `Snake case <https://en.wikipedia.org/wiki/Snake_case>`_ format (``$cart_content``).

2. Names **must** be meaningful and informative.

  * **Right:**

    ::

        $counter, $user_id, $product, $is_valid

  * **Wrong:** 

    ::

        $с, $uid, $obj, $flag

3. Variables that store the list of multiple objects of the same type **should** have the ``_list`` suffix, for example: ``$products_list``, ``$cart_applied_promotions_list``. That way it’s easier to determine which variable stores the list and which variable stores an element of the list. Take this array iteration in the ``foreach`` cycle, for example:

  * **Right:** 

    ::

        foreach ($applied_promotion_list as $applied_promotion) {
            // the variables are easy to distinguish
        }

  * **Wrong:**

    ::

        foreach ($applied_promotions as $applied_promotion) {
                // it is easy to mistake $applied_promotions for $applied_promotion when you look through the code
        }

4. Variables that store boolean values **should** have prefixes such as ``is_``, ``has_``, or any other appropriate verb.

  * **Right:**

    ::

        $is_valid, $has_rendered, $has_children, $use_cache

  * **Wrong:** 

    ::

        $valid, $render_flag, $parentness_status, $cache

5. Names of the variables **shouldn’t** begin with underscore. There were cases when one function included the ``$cache``, ``$_cache`` and ``$__cache`` variables.

------------------------------
Naming and Declaring Constants
------------------------------

1. Write constant names **entirely in uppercase**; use underscore (``_``) to separate elements: ``SORT_ORDER_ASC``, ``COLOR_GREEN``.

2. Multiple constants of the same type **should** have the repeating part in their names come first:

  * **Right:**

    ::

        COLOR_GREEN, COLOR_RED; SORTING_ASC, SORTING_DESC

  * **Wrong:** 

    ::

        GREEN_COLOR, RED_COLOR; ASC_SORTING, DESC_SORTING

3. The names must be meaningful and informative.

---------------
String Literals
---------------

1. When you refer to an element of the array by key, enclose the name of the key in single quotation marks: ``$product['price'];``.

2. Enclose all the string variables, that don’t contain other variables, in single quotation marks: ``$foo = 'bar';``.

3. If a string must include the value of the variable, enclose the string in double quotation marks and put the name of the variable in curly brackets: ``$greeting_text = "Hello, {$username}!";``.

---------------------------------------
Magic Numbers in the Middle of the Code
---------------------------------------

1. Don’t use numeric values and string literals in the code, unless it’s  evident what they stand for.
  
  **Wrong:**

  ::
 
    $product->tracking = 'O'; // What does 'O' mean?
    ...
    $order_status = 'Y'; // "Y" == "Yes"? "Yellow"?

2. To avoid magic numbers and string literals in the code, assign them to constants with meaningful names. Refer to those constants in the code.

3. There may be a group of values, like the possible values of the field in the database. The constants of these values must use a separate class in the ``Tygh\Enum`` namespace. The example of such class is ``Tygh\Enum\ProductTracking``. It looks as follows:

  ::

    $product->tracking = Tygh\Enum\ProductTracking::TRACK_WITH_OPTIONS;

----------
Commenting
----------

1. Write comments in English only. Use double slash (``//``) to comment the code in the function or in the controller;

2. **Don’t** use perl style(#)—it is not allowed;

3. Don’t make comments about something that is easy to figure out by looking at the code. The code without comments is better than the code with incorrect and irrelevant comments.

4. Keep your comments brief and to the point.

------
PHPDoc
------

1. **Try** to comply with the draft of `PSR-5 <https://github.com/phpDocumentor/fig-standards/blob/master/proposed/phpdoc.md>`_—once the standard is accepted, it will become **mandatory**.

2. You **must** use the block with the commentary and the description of the arguments for declaring all functions, methods, classes and class properties.

3. If a function doesn’t return a value, it’s better to use ``@return void`` or not to write ``@return`` at all.

4. Here’s the example of formatting done right:

  ::

      /**
       * Generates date-time intervals of a given period for sales reports
       *
       * @param int $interval_id    Sales reports interval ID
       * @param int $timestamp_from Timestamp of report period beginning date
       * @param int $timestamp_to   Timestamp of report period end date
       * @param int $limit
       *
       * @return array
       */

-----------
Performance
-----------

**Try to avoid** using the ``Registry::get()`` summon in the body of the cycle. This operation requires a lot of resources, and addressing the storage lowers the performance significantly. To avoid cyclic calling, assign the value from ``Registry`` to the variable before the cycle, and use the variable within the cycle.

=========
Functions
=========

-----
Names
-----

Function names must be written in lowercase and begin either with ``fn_``, or with ``db_``:

  ::

      fn_get_addon_option_variants

---------
Arguments
---------

If several arguments have standard values, or the arguments aren’t primary, unite them in one ``$params`` array. That way you only pass the primary arguments and the ``$params`` array to the function.

Here’s the example of how you can change the code:

  ::

      // before
      function fn_get_product_data($product_id, &$auth, $lang_code = CART_LANGUAGE, $field_list = '', $get_add_pairs = true, $get_main_pair = true, $get_taxes = true, $get_qty_discounts = false, $preview = false, $features = true, $skip_company_condition = false)


      // after
      function fn_get_product_data($product_id, &$auth, $params)
      {
          $default_params = array(
              'lang_code' => CART_LANGUAGE,
              'field_list' => '',
              'get_add_pairs' => true,
              'get_main_pair' => true
              'get_taxes' => true,
              'get_qty_discounts' = false,
              'preview' = false,
              'get_features' = true
          )
          $params = fn_array_merge($default_params, $params);

---------------------------
DRY - Don't Repeat Yourself
---------------------------

The code that appears in two or more places in a controller or a function, it **must** be made a separate function.

---------------------------
Returning the Value is Good
---------------------------

Unless you work with a hook processor function, **try not to** pass variables to the function by reference, modifying the value of the variable without the function returning anything. This can lead to unaccountable and non-evident changes to the values of variables. Save your own time and the time of your colleagues that you’d otherwise spend on debugging.

.. important::

    Passing variables by reference doesn’t reduce memory consumption: PHP optimizes everything even if you pass variables by value. 

----------
Exit Point
----------

A function **should** have only one exit point. Two or more exit points are acceptable in the following cases:

  * it reduces code branching (it’s better to have multiple ``return`` than 5 nested ``if``)
  * it saves resources (that is the case with ``fn_apply_exceptions_rules`` in **fn.catalog.php**)

-----------------------------
Comments on deleted functions
-----------------------------

This comment is added to deprecated functions. The content of such functions is replaced by a notification::

  <?php


  /**
   * This function is deprecated and no longer used.
   * Its reference is kept to avoid fatal error occurrences.
   * 
   * @deprecated deprecated since version 3.0
   */
  ?>

For example::

  <?php

  /**
   * This function is deprecated and no longer used.
   * Its reference is kept to avoid fatal error occurrences.
   * 
   * @deprecated deprecated since version 3.0
   */
  function fn_get_setting_description($object_id, $object_type = 'S', $lang_code = CART_LANGUAGE)
  {
          fn_generate_deprecated_function_notice('fn_get_setting_description()', 'Settings::get_description($name, $lang_code)');
          return false;
  }
  ?>

--------------------------------------
Comments on frequently used parameters
--------------------------------------

These are approved comments to describe variables in the code. Use these comments when defining a hook where it seems appropriate::

  $auth - Array of user authentication data (e.g. uid, usergroup_ids, etc.)
  $cart - Array of the cart contents and user information necessary for purchase
  $lang_code - 2-letter language code (e.g. 'en', 'ru', etc.)
  $product_id - Product identifier
  $category_id - Category identifier
  $params - Array of various parameters used for element selection
  $field_list - String of comma-separated SQL fields to be selected in an SQL-query
  $join - String with the complete JOIN information (JOIN type, tables and fields) for an SQL-query
  $condition - String containing SQL-query condition possibly prepended with a logical operator (AND or OR)
  $group_by - String containing the SQL-query GROUP BY field

===========================
Object-Oriented Programming
===========================

------------
Entity Names
------------

1. The names of classes, interfaces and traits **must** begin with an uppercase letter and follow `CamelCase <https://en.wikipedia.org/wiki/CamelCase>`_.

2. The names of abstract classes **must** begin with ``A``, for example: ``ABackend``, ``ADatabaseConnection``.

3. The names of the interfaces **must** begin with ``I``, for example: ``ICountable``, ``IFilesystemDriver``.

4. If the name of a class, interface, trait or method has an acronym like **URL**, **API**, **REST** etc., then the acronym **must** follow the rules of **CamelCase**.

  * **Right:** 

    ::

        $a->getApiUrl(), $a = new Rest();, class ApiTest
 
  * **Wrong:** 

    ::

        $a->getAPIURL(), $a = new REST();, class APITest

---------
Constants
---------

The naming rules are the same as for constants outside of classes. Here’s an example:

  ::

      class Api
      {
          /**
           * Default HTTP request format mime type
           *
           * @const DEFAULT_REQUEST_FORMAT
           */
          const DEFAULT_REQUEST_FORMAT = 'text/plain';

----------
Properties
----------

1. The naming rules are the same as for variables.

2. **Don’t** begin the names of private and protected properties with underscore (``_``).

  For example:

  ::

      class Api
      {
          /**
           * Current request data
           *
           * @var Request $request
           */
          private $request = null;

          /**
           * Sample var
           *
           * @var array $sample_var
           */
          private $sample_var = array();

-------
Methods
-------

1. Unlike functions, method **names must begin with a lowercase** letter and follow **camelCase**.

2. **Don’t** begin the names of private and protected methods with underscore (``_``).

3. **Try** to group the methods in the class by visibility area: ``public -> protected -> private``.

  For example:

  ::

      class ClassLoader
      {
          /**
           * Creates a new ClassLoader that loads classes of the
           * specified namespace.
           *
           * @param string $include_path Path to namespace
           */
          public function __construct($include_path = null)
          {
              // ...
          }

          /**
           * Gets request method name (GET|POST|PUT|DELETE) from current http request
           *
           * @return string Request method name
           */
          private function getMethodFromRequestHeaders()
          {
              // ...
          }

----------
Namespaces
----------

**Tygh** is the name of the namespace that contains all the namespaces and core classes of CS-Cart.

1. Every class, interface, and trait of the core and add-ons **must** belong to this namespace.

2. If several classes, interfaces, or traits are related to some specific functionality, they must belong to a common subspace, such as block manager classes (``Tygh\BlockManager``) or REST API (``Tygh\Api``).

3. Every file that uses classes, interfaces, or traits **must** have the ``use`` directive at the beginning to specify the namespaces used in the file. If the names of classes from different namespaces match, there **must** be descriptions of aliases for the names of the conflicting classes (``use \Tygh\BlockManager\RenderManager as BlockRenderer``).

4. Every entity, be it a class, interface, or trait, **must** be in a separate file. Developers often break this rule when they declare both a class and an exception in the same file.

5. Add-ons **should** add their classes, interfaces and traits **only to their own namespace** ``\Tygh\Addons\AddonName``. For example, the allowed namespace for the ``form_builder`` add-on is ``\Tygh\Addons\FormBuilder``. 

  This rule has 2 exceptions:

  * when you add a new API entity, add the class to ``\Tygh\Api\Entities``
  * when you add new connectors for the Upgrade Center, add the class to ``\Tygh\UpgradeCenter\Connectors``

6. **Remember** that the root directory of every installed and active add-on also loads classes automatically. The ``\Foo\Bar\MyClass`` class in **app/addons/my_changes/Foo/Bar/MyClass.php** can and will be loaded automatically when summoned in the code like this: ``$my_class_instance = new \Foo\Bar\MyClass();``.

7. The ``use`` directives **must** be grouped with each other. For example:

  ::

      use Tygh\Registry;
      use Tygh\Settings;
      use Tygh\Addons\SchemesManager as AddonSchemesManager;
      use Tygh\BlockManager\SchemesManager as BlockSchemesManager;
      use Tygh\BlockManager\ProductTabs;
      use Tygh\BlockManager\Location;
      use Tygh\BlockManager\Exim;

---------------
Design Patterns
---------------

You shouldn’t create singleton classes and the classes consisting of static methods. It is next to impossible to write unit tests for that kind of code.

===========
SQL Queries
===========

1. Structure the query as follows:

  .. important::

      The proper placement of quotation marks and dots does matter.

  ::

      $partner_balances = db_get_hash_array(
          "SELECT pa.partner_id, u.user_login, u.firstname, u.lastname, u.email, SUM(amount) as amount"
          . " FROM ?:aff_partner_actions as pa"
          . " LEFT JOIN ?:users as u ON pa.partner_id = u.user_id"
          . " LEFT JOIN ?:aff_partner_profiles as pp ON pa.partner_id = pp.user_id"
          . " LEFT JOIN ?:affiliate_plans as ap ON ap.plan_id = pp.plan_id AND ap.plan_id2 = pp.plan_id2"
              . " AND ap.plan_id3 = pp.plan_id3"
          . " WHERE pa.approved = 'Y' AND payout_id = 0 ?p ?p"
          . " ORDER BY $sorting $limit",
          'partner_id', $condition, $group
      );

2. The closing bracket **must** be on the new line. That way you organize the code into blocks and make it more readable.

3. The data you use in the queries **must** be inserted via placeholders. **Never** insert the values of variables into the query directly.

4. If the SQL query has several parts that are stored in different variables, every part **must** be wrapped in the ``db_quote`` function summon. That prevents confusion with placeholders. 

5. Parts of the SQL query text **should** be inserted with the ``?p`` placeholder.

6. Here’s the example of the two previous points:

  ::

         $joins = array();

          // Every part of the query is wrapped in db_quote(), regardless of whether placeholders are necessary
          $joins[] = db_quote(' LEFT JOIN `foo` AS `f` ON `f`.`product_id` = `products`.`product_id`');
          $joins[] = db_quote(' LEFT JOIN `bar` AS `b` ON `b`.`product_id` = `products`.`product_id` AND `b`.`order_id` = ?n', $order_id);

          $query = db_quote(
              'SELECT * FROM `products`'
              . ' WHERE `products`.`status` = "A"'
              . ' ?p', // the joins list is inserted into the query with the "?p" placeholder 
              implode(' ', $joins)
          );

7. Learn more about placeholders and working with them in :doc:`the dedicated article <../db/placeholders>`.

=============
General Rules
=============

1. **Don’t** silence PHP errors with the **@** operator.

2. There must be **no errors from the PHP interpreter**, such as Warnings, Notices etc. Non-existing variables, wrong data types, etc., must be handled in the code.

3. Unless you know for certain where the internal pointer in the array is, **don’t** use the ``current()`` and ``each()`` functions. If you want to get the first element in the array, use the ``reset()`` function.

4. **Don’t** use ``HTTP_REFERER``. If you want to make a redirect to the previous location, pass the ``redirect_url``.
