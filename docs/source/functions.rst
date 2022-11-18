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
  
  
``Input()`` function and ``setInputDefault()``:
-------------------------------------------------
  
The ``Input()`` function holds the interactive assumptions data, which the user is able tweak and play around with.

We usually use UPPERCASE when defining INPUT variables, so that we know it is referring to an input, but you can use whichever case you want.

The variable name will be formatted like so:

  ``INPUT.NUMBER_ONE`` -> Number One
  ``INPUT.Number_Two`` -> Number Two
  ``INPUT.number_three`` -> Number Three

Use '_' to refer to a rate:
  ``INPUT._RATE: 10`` -> Will translate to 10% or 0.1
 
Use ``setInputDefault()`` to set an INPUT value:
  ``INPUT.CALCULATED: ''``
  
  ``setInputDefault('CALCULATED', 10);``

.. code-block:: javascript

  var INPUT = Input({NUMBER: 5,  // Plain number 5
                     CALCULATED_NUMBER: '',  // Number will be calculated later on
                     _RATE: 5,  // Plain rate 5%
                     _CALCULATED_RATE: '',  // Rate that will be calculated later on
                    }); 
  $.when().done(
      function(){
        // Set the INPUT.CALCULATED_NUMBER to 1.23
        setInputDefault('CALCULATED_NUMBER', 1.23);

        // Set the INPUT._CALCULATED_RATE to 1.23%
        setInputDefault('_CALCULATED_RATE', 1.23);
        
        print(INPUT.NUMBER, 'INPUT.NUMBER');
        >>> INPUT.NUMBER: 5 
        
        print(INPUT.CALCULATED_NUMBER, 'INPUT.CALCULATED_NUMBER');
        >>> INPUT.CALCULATED_NUMBER: 1.23 
        
        print(INPUT._RATE, 'INPUT._RATE');
        >>> INPUT._RATE: 0.05 
        
        print(INPUT._CALCULATED_RATE, 'INPUT._CALCULATED_RATE');
        >>> INPUT._CALCULATED_RATE: 0.0123 
  });

.. note::

   Please note that this section is currently under active development.
