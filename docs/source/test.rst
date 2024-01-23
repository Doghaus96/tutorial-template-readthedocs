Test
=====

.. _Heading 1:

Heading 1
------------

test code block with bash:

.. code-block:: bash

   sreport cluster AccountUtilizationByUser user=$(whoami) Start=$(date -d "-1 month" +%Y-%m-%d)

test code block with lua:

.. code-block:: lua


  local installRoot = "/usr/local"
  local modName = myModuleName() -- Based on folder name (ex: gcc)
  local modVersion = myModuleVersion() -- Based on file name (ex: 4.8)
  local modfilePath = myFileName()


Creating recipes
----------------

To retrieve a list of random ingredients,
you can use the ``lumache.get_random_ingredients()`` function:

.. autofunction:: lumache.get_random_ingredients

The ``kind`` parameter should be either ``"meat"``, ``"fish"``,
or ``"veggies"``. Otherwise, :py:func:`lumache.get_random_ingredients`
will raise an exception.

.. autoexception:: lumache.InvalidKindError

For example:

>>> import lumache
>>> lumache.get_random_ingredients()
['shells', 'gorgonzola', 'parsley']


.. _test2:
Second Heading test
--------------

.. code-block:: bash 

  whoami
  
