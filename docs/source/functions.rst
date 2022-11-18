Valuation functions
====================

The following functions are found in `DiscountingCashFlows/Documentation/source-code/valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions.js>`__. We will cover each function and its use cases.

``$.when().done()`` function:
---------------------

The valuation engine is built on the popular JS Framework 'jQuery'. Don't worry if you've never used it before because the ``$.when().done()`` function is the only function that we will use from the framework in our valuations.

To understand what the ``$.when().done()`` function does, we need to think about what is the workflow of valuing a company. 
 
Step 1. Gather financial data for the company (get the income statements, the balance sheets, the cashflow reports, dividends, etc. )

Step 2. Process the data via a valuation model to calculate an intrinsic value of the company.

The ``$.when().done()`` function takes care of Step 1. It retrieves the financial data from our :doc:`api` just like in the following example:

.. code-block:: javascript

  $.when(
    get_income_statement(),
    get_balance_sheet_statement(),
    get_profile(),
    get_dividends_annual(),
    get_treasury(),
    get_fx()).done(
    function(_income, _balance, _profile, _dividends, _treasury, _fx){

    // Here we take care of Step 2. Process the data to calculate an intrinsic value of the company.
    // ...

  });


So, why do we use ``$.when().done()`` anyway and not some other function like ``$.get()``? 

The advantage of using ``$.when().done()`` is that we submit all requests at once, instead of one by one.

You can think about it like going to the grocery:

 Method 1: You can purchase item1, return home, go back to the grocery, purchase item2, return home, go back to the grocery store, purchase the last item3 and return home and then start cooking.
 Method 2: You can also (and probably should) go to the grocery and purchase item1, item2 and item3 simultaneously and then return home with all the groceries and start cooking.
 
So, you'd probably want to stick to method 2 because it saves a lot of time. And that's exactly what ``$.when().done()`` does. It retrieves all data at once and once it is ``done()``, it can begin processing the data.

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
 
 
Setting assumptions:
--------------------

Assumptions are set either static or dynamically.

- ``static`` : We know what the assumption will be (Example: INPUT.GROWTH_YEARS: 5 - it will be 5 growth years by default)

- ``dynamic``: We need to calculate or retrieve the data from the API (Example: INPUT._TREASURY_YIELD: '' - needs to be filled with the us 10 year treasury yield using ``setInputDefault()`` function down below)

``Input()`` function:
*********************
  
The ``Input()`` function holds the interactive assumptions data, which the user is able tweak and play around with.

We usually use UPPERCASE when defining INPUT variables, so that we know it is referring to an input, but you can use whichever case you want.

The variable name will be formatted like so:

  ``INPUT.NUMBER_ONE`` -> Number One
  
  ``INPUT.Number_Two`` -> Number Two
  
  ``INPUT.number_three`` -> Number Three

Use '_' as the first character when referring to a rate:

  ``INPUT._RATE: 10`` -> Will translate to 10% or 0.1
 
``setInputDefault()`` function:
*******************************

Use ``setInputDefault()`` to set a blank INPUT assumption dynamically. 

For example, if we wanted to set an assumption (``INPUT._TREASURY_YIELD``) to the Yield of the US 10 Year Treasury Bond. Assume we've got the treasury data in object ``treasury``.

  ``INPUT._TREASURY_YIELD: ''``
  
  ``setInputDefault('_TREASURY_YIELD', treasury['year10']);``

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

Displaying a Chart:
-------------------

``fillHistoricUsingReport()`` function:
***************************************

``fillHistoricUsingList()`` function:
***************************************

``forecast()`` function:
************************

``renderChart()`` function:
***************************************

Displaying a Table:
-------------------

``toColumn()`` function:
***************************************

Utility functions:
-------------------

``currencyRate()`` function:
***************************************

``newArrayFill()`` function:
***************************************

``arrayValuesToRates()`` function:
***************************************

``getArraySum()`` function:
***************************************

``getGrowthRateList()`` function:
***************************************

``addKey()`` function:
***************************************

``linearRegressionGrowthRate()`` function:
***************************************

``averageMargin()`` function:
***************************************

``averageGrowthRate()`` function:
***************************************

``applyMarginToList()`` function:
***************************************

``getGrowthList()`` function:
***************************************

``toM()`` function:
***************************************

``toK()`` function:
***************************************

``monitor()`` function:
***************************************

``Description()`` function:
***************************************

.. note::

   Please note that this section is currently under active development.
