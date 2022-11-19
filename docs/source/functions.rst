Valuation functions
====================

The following functions are found in `DiscountingCashFlows/Documentation/source-code/valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions.js>`__ with the exception of ``$.when().done()`` function. We will cover each function and its use cases.

``$.when().done()`` function:
---------------------

The valuation engine is built on the popular JS Framework 'jQuery'. Don't worry if you've never used it before because the ``$.when().done()`` function is the only function that we will use from the framework in our valuations.

To understand what the ``$.when().done()`` function does, we need to think about what the workflow of valuing a company is. 
 
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

 Method 1: You can go to the grocery, purchase item1, return home, go back to the grocery, purchase item2, return home, go back to the grocery store, purchase the last item3 and return home and then start cooking.
 
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

Assumptions are set either statically or dynamically.

- ``static`` : We have a default value for the assumption (Example: INPUT.GROWTH_YEARS: 5 - it will be 5 growth years by default)

- ``dynamic``: We can set the assumption by using ``setInputDefault()`` (Example: INPUT._TREASURY_YIELD: '' - needs to be filled dynamically with the us 10 year treasury yield)

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

Here is a code example of defining and setting assumptions:

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

The following functions can be used to display a chart to the screen. The flow of creating a chart is:

 #. Step 1. Fill the historic data (for example the company's historic revenues, net income, etc.) by using either ``fillHistoricUsingReport()`` or ``fillHistoricUsingList()``, whichever you find easier to setup.
 
 #. Step 2. Fill the forecasted data by using ``forecast()``. This data represents the 'assumptions' for the chart (for example predicting future revenues).
 
 #. Step 3. Be sure to render the chart or else it won't be shown on the screen.

``fillHistoricUsingReport()`` function:
***************************************

This function makes things really quick and easy when you want to add historic financial data in the chart from an existing report(income statement, balance sheet, etc.).

Arguments of ``fillHistoricUsingReport(report, key, measure)``

 * ``report`` - The report object from the API. For example: income statement.
 
 * ``key`` - This is the historic data series key that you'll want to fill the chart with (for historic revenues use key 'revenue')
 
 * ``measure`` - Has 3 options: 'M', 'K' or left blank. 
 
  * Use 'M' when you want to format the numbers to millions (divide by 1,000,000). 
  
  * Use 'K'when you want to format the numbers to thosands (divide by 1,000).
  
  * Leave blank when you don't want any number formatting.

Example:

.. code-block:: javascript
 
 // Adds the full history of eps from the income statements
 fillHistoricUsingReport(income, 'eps');
 
 // Adds the revenues, formatted to millions, of the last 10 years of income statements
 fillHistoricUsingReport(income.slice(0,10), 'revenue', 'M');
 
``fillHistoricUsingList()`` function:
***************************************

This function is used when we want to add a list to the chart.

Arguments of ``fillHistoricUsingList(list, key, endingYear)``

 * ``list`` - The list of historic values that will be added to the chart (Example: [1, 2, 3, 4])
 
 * ``key`` - This is the historic data series key that you'll want to fill the chart with (for historic revenues use key 'revenue').
 
 * ``endingYear`` - This is the year when the list ends. 
 
.. note::
 
 Specify only if ``fillHistoricUsingReport()`` was not used before. If ``fillHistoricUsingReport()`` has been used, then the ending year will be the report's ending year.

Example with ``endingYear``:

.. code-block:: javascript
 
 // Adds to the chart the data series [1, 2, 3, 4] labeled as 'My List' ending in year 2022
 fillHistoricUsingList([1, 2, 3, 4], 'myList', 2022);
 
Example without ``endingYear``:
 
.. code-block:: javascript

 // The ending year will be the report's ending year.
 fillHistoricUsingReport(income.slice(0,10), 'revenue', 'M');
 fillHistoricUsingList([1, 2, 3, 4], 'myList');
 
``forecast()`` function:
************************

The forecast function adds forecasted points to the chart. These points can be considered as 'assumptions' on the chart. For example, we could project the next 10 years of free cash flow and, by using the forecast function, we can make each forecasted point draggable and editable in the forecast table.

.. note::

 The forecasted points on the chart also have a forecast table right underneath the chart, where each forecasted point of the chart is linked to a cell in the table.

.. warning::
 To use the ``forecast()`` function correctly, you need to have filled some historic data, either by using ``fillHistoricUsingReport()`` or ``fillHistoricUsingList()``. This is for the function to know the starting year of the forecast.

Arguments of ``forecast(list, key, settings)``

 * ``list`` - The list of forecasted points that will be added to the chart (Example: [1, 2, 3, 4]).
 
 * ``key`` - This is the key of the data series you are trying to forecast (for forecasting revenues use key 'revenue').
 
 * ``settings`` - Has 2 options: 'chartHidden' or left blank.
 
  * 'chartHidden' is for hiding values from being displayed in the chart. This is useful when we need to forecast rates and ratios, that are too small to be displayed on the chart.
  
  * Leave blank if you want to display the forecasted list to the chart.

Example:

.. code-block:: javascript

 // TODO...

``renderChart()`` function:
***************************

Some technical explanation for when the rendering happens: 

 In  `valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions.js>`__ there is a global object that stores all chart data called ``_chart_data`` which has the following members:
  * ``x_historic`` - This is the X-Axis for the historic data, where we usually store years (for example 2012 - 2022, a historic period of 10 years).
  * ``x_forecasted`` - This is the X-Axis for the forecasted data (for example 2022 - 2032, a future period of 10 years).
  * ``y_historic`` - This is the Y-Axis for historic data, where we store historic revenue, net income, etc.
  * ``y_forecasted`` - This is the Y-Axis for future data (future revenue, net income, etc.).
  * ``y_forecasted_chart_hidden`` - This is not actually visible on the chart. It is a part of the forecast table. Please, refer to the ``forecast()`` section.
  * ``name`` - This is the title of the chart.
  * ``hidden_series`` - This controls the visibility of individual data series (when we hide revenues for example, it will be stored here).

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
