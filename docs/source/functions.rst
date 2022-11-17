Valuation functions
====================

The following functions are found in `DiscountingCashFlows/Documentation/source-code/valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions.js>`__. We will cover each function and its use cases.

.. _print-function:

``print()`` function:
---------------------

The ``print()`` function prints values and messages to the screen. Below are examples of usage types:

.. code-block:: javascript

  // Prints a message with a label
  print('Hello World!', 'First Message');
  >>> First Message: Hello World! 
  
  // This is how you would display an error
  print('This is how you display an error', 'Error');
  >>> Error: This is how you display an error
  
  // Displaying a warning
  warning('You have been warned!');
  >>> Warning: You have been warned! 
  
  // This is a plain value
  print(1.23456, 'Plain value');
  >>> Plain value: 1.23456 
  
  // This is a value with numeric format (3 decimals maximum)
  print(1.23456, 'Formatted Value', '#');
  >>> Formatted Value: 1.235 
  
  // Numeric format includes thousands(K) and millions(M)
  print(1000, '1 Thousand', '#');
  >>> 1 Thousand: 1 K 
  print(1000000, '1 Million', '#');
  >>> 1 Million: 1 Mil. 
  
  // Add a 4th argument for currency
  print(12.34, 'Price', '#', 'USD');
  >>> Price: 12.34 USD
  
  // When dealing with rates, specify '%' for rate formatting
  print(1.23, 'Rate', '%');
  >>> Rate: 123.00% 
  
  
.. note::

   Please note that this is under active development.
