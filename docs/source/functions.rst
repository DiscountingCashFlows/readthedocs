Valuation functions
====================

The following functions are found in `DiscountingCashFlows/Documentation/source-code/valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions.js>`__. We will cover each function and its use cases.

.. _print-function:

``print()`` function:
---------------------

The ``print()`` function prints to the screen. Below are examples of usage types:

.. code-block:: console

  // Prints "Message: Hello World!"
  print('Hello World!', 'First Message');
  
  // This is how you would display an error
  print('This is how you display an error', 'Error');
  
  // This is a plain number
  print(1.23, 'INPUT.NUMBER', '#');
  
  // When dealing with values, specify '#' for number and currency
  print(1.23, 'Value', '#', currency);  // 1.23 USD
  
  // When dealing with rates, specify '%' for rates
  print(1.23, 'Rate', '%');  // 123% 
  
.. note::

   Please note that this is under active development.
