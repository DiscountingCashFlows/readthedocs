Valuation functions
====================

Some of the following functions can also be found in `DiscountingCashFlows/Documentation/source-code/valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions.js>`__. We will cover each function and its use cases.

.. note::

   Please note that this section is currently under active development. Some functions may not be up to date. If you find any outdated functions, please let us know `here <https://discountingcashflows.com/help/>`__

``$.when().done()`` function:
-----------------------------

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


``deepCopy()`` function:
------------------------

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L337>`__

Creates a deep copy of the object that has been parsed and retrieves the underlying data.

In JavaScript, objects and arrays are mutable by default. Deep copying means cloning the original object into an identical copy, which you can modify without altering the original object.

.. warning::

   Always create copies of your response objects. Editing the response objects directly can cause caching issues if you ever decide to add your model to your watchlist or set up notifications.

Arguments of ``deepCopy(object)``

 * ``object`` - The response object
 
.. code-block:: javascript

   $.when(
       get_income_statement(),
       get_balance_sheet_statement(),
       get_profile(),
       get_dividends_annual(),
       get_treasury(),
       get_fx()).done(
       function(_income, _balance, _profile, _dividends, _treasury, _fx){
         // Create deep copies of response objects
         var income = deepCopy(_income);
         var balance = deepCopy(_balance);
         var profile = deepCopy(_profile);
         var dividends = deepCopy(_dividends);
         var treasury = deepCopy(_treasury);
         var fx = deepCopy(_fx);
     });
 
Displaying Messages
-------------------

``print()`` function:
*********************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L1007>`__

Prints values and messages to the screen. Below are examples of usage types:

Arguments of ``print(str, label='', type='', currency='')``

 * ``str`` - The actual message that will be printed to the screen.
 
 * ``label`` - The label of the print message. Leave blank for no label.
 
 * ``type`` - Has 2 options: '#' for number formatting or '%' for rate formatting. Leave blank for no formatting.
 
 * ``currency`` - Can be either a currency (USD, EUR), or '%'. Leave blank for no currency.

.. code-block:: javascript

  // Prints a message with a label
  print('Hello World!');
  >>> Hello World!
  
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
 
``warning()`` function:
***********************
 
Display a warning alert message (in yellow) on the top of the model.
 
.. code-block:: javascript

   // Displaying a warning
   warning('You have been warned!');
   >>> Warning: You have been warned!
 
``error()`` function:
***********************
 
Display an error alert message (in red) on the top of the model.
 
.. code-block:: javascript

   // Displaying a warning
   error('Something went wrong! :(');
   >>> Error: Something went wrong! :(

``Description()`` function:
***************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L217>`__

The ``Description()`` function serves as a quick readme for the model and it is shown at the top of each model. It supports html formatting, so you can style it any way you want.

.. code-block:: javascript

 Description(`<h5>Base Model Code</h5>
             <p>This is the base code for writing valuation models.</p>
             <p class='text-center'>Read more: <a href='https://github.com/DiscountingCashFlows/Documentation/' target='_blank'><i class="fab fa-github"></i> GitHub Documentation</a></p>
             `);

   
Setting an Estimated Value
---------------------------

**What is the estimated value?**

Every valuation model needs to output an **estimated value** based on future prospects of the company or some other method.

For example, the `Discounted Free Cash Flow Model <https://discountingcashflows.com/company/AAPL/valuation/default/3/>`__ takes in some financial data and some assumptions, processes them and comes up with an estimated value of the company, per share. You can see it at the top of the model "$AAPL Estimated Value in USD ...".

``_SetEstimatedValue()`` function:
**********************************

Set the estimated value of a company at the top of the model (this is only visible in Company Valuation and not in Model Code Editor).

Arguments of ``_SetEstimatedValue(value, currency)``

 * ``value`` - The estimated value.
 
 * ``currency`` - The currency of the estimated value.
 
.. code-block:: javascript
 
   $.when().done(
     function(){
       // Sets the value at the top of the model to 123 USD
       _SetEstimatedValue(123, 'USD');
   });
 
To see the example code in action, save the code and go to Models Dropdown -> Open in Company Valuation.

``_StopIfWatch()`` function:
****************************

This function is built specfifically for watches and notifications (from the Watchlist & Notifications page).

A watchlist item or a notification item does not need to print messages or show charts and tables, because nobody is going to see them anyway.

The only purpose of watches and notifications is to evaluate a given valuation model and show an estimated value. When the code has reached an estimated value, it can stop right away and return.

Arguments of ``_StopIfWatch(value, currency)``

 * ``value`` - The estimated value.
 
 * ``currency`` - The currency of the estimated value.
 
.. code-block:: javascript

 $.when().done(
   function(){
     // If this code is being run by a watch or a notification interpreter
     // then, it will stop right here and not print anything.
     if(_StopIfWatch(123, 'USD')){
       return;
     }
     _SetEstimatedValue(123, 'USD');
     print('Some information...');
  }); 
 
But, if we run the code in the Model Code Editor, we will see:

.. code-block:: javascript

   >>> Some information...

Setting assumptions
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

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L988>`__

Use ``setInputDefault()`` to set a blank INPUT assumption dynamically. 

For example, if we wanted to set an assumption (``INPUT._TREASURY_YIELD``) to the Yield of the US 10 Year Treasury Bond. Assume we've got the treasury data in object ``treasury``.

  ``INPUT._TREASURY_YIELD: ''``
  
  ``setInputDefault('_TREASURY_YIELD', treasury['year10']);``

Here is a code example of defining and setting assumptions:

.. code-block:: javascript

  var INPUT = Input({NUMBER: 5,  // Static Assumption: Number 5
                     CALCULATED_NUMBER: '',  // Dynamic Assumption (will be calculated later on)
                     _RATE: 5,  // Static Assumption: Rate 5%
                     _CALCULATED_RATE: '',  // Dynamic Assumption Rate (will be calculated later on)
                    }); 
  $.when().done(
      function(){
        // Set the dynamic assumption number
        setInputDefault('CALCULATED_NUMBER', 1.23);

        // Set the dynamic assumption rate
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

Displaying a Chart
-------------------

The flow of creating a chart is:

 #. Step 1. Fill the historic data (for example the company's historic revenues, net income, etc.) by using either ``fillHistoricUsingReport()`` or ``fillHistoricUsingList()``, whichever you find easier to setup.
 
 #. Step 2. Fill the forecasted data by using ``forecast()``. This data represents the 'assumptions' for the chart (for example predicting future revenues).
 
 #. Step 3. Be sure to render the chart or else it won't be shown on the screen.

``fillHistoricUsingReport()`` function:
***************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L835>`__

Adds a data series ('revenue', 'netIncome') to the chart from a given report. This function makes things really quick and easy when you want to add historic financial data in the chart from an existing report(income statement, balance sheet, etc.).

Arguments of ``fillHistoricUsingReport(report, key, measure)``

 * ``report`` - The report object from the API. For example: income statement.
 
 * ``key`` - This is the historic data series key that you'll want to fill the chart with (for historic revenues use key 'revenue')
 
 * ``measure`` - Has 3 options: 'M', 'K' or left blank. 
 
  #. Use 'M' when you want to format the numbers to millions (divide by 1,000,000). 
  
  #. Use 'K'when you want to format the numbers to thosands (divide by 1,000).
  
  #. Leave blank when you don't want any number formatting.

Example:

.. code-block:: javascript
 
   $.when(
     get_income_statement()).done(
     function(_income){
       var income = deepCopy(_income);
       // Adds the full history of eps from the income statements
       fillHistoricUsingReport(income, 'eps');

       // Adds the revenues, formatted to millions, of the last 10 years of income statements
       fillHistoricUsingReport(income.slice(0,10), 'revenue', 'M');

       renderChart('Example chart');
   });
 
``fillHistoricUsingList()`` function:
***************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L854>`__

Adds a list to the chart.

Arguments of ``fillHistoricUsingList(list, key, endingYear)``

 * ``list`` - The list of historic values that will be added to the chart (Example: [1, 2, 3, 4])
 
 * ``key`` - This is the historic data series key that you'll want to fill the chart with (for example: use key 'revenue' for historic revenues).
 
 * ``endingYear`` - This is the year when the list ends. 
 
.. note::
 
 Specify only if ``fillHistoricUsingReport()`` was not used before. If ``fillHistoricUsingReport()`` has been used, then the ending year will be the report's ending year.

Example with ``endingYear``:

.. code-block:: javascript
 
 // Adds to the chart the data series [1, 2, 3, 4] labeled as 'My List' ending in year 2022
 fillHistoricUsingList([1, 2, 3, 4], 'myList', 2022);
 renderChart('Example chart');
 
Example without ``endingYear``:
 
.. code-block:: javascript

   $.when(
     get_income_statement()).done(
     function(_income){
       var income = deepCopy(_income);

       // The ending year will be the report's ending year.
       fillHistoricUsingReport(income.slice(0,10), 'revenue', 'M');
       fillHistoricUsingList(newArrayFill(10, 100000), 'myList');
       renderChart('Example chart');
   });
 
``forecast()`` function:
************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L876>`__

Adds forecasted points to the chart. These points can be considered as 'assumptions' on the chart. For example, we could project the next 10 years of free cash flow and, by using the forecast function, we can make each forecasted point draggable and editable in the forecast table.

.. note::

 The forecasted points on the chart also have a forecast table right underneath the chart, where each forecasted point of the chart is linked to a cell in the table.

.. warning::

 To use the ``forecast()`` function correctly, you need to have filled some historic data, either by using ``fillHistoricUsingReport()`` or ``fillHistoricUsingList()``. This is for the function to know the starting year of the forecast.

Arguments of ``forecast(list, key, settings)``

 * ``list`` - The list of forecasted points that will be added to the chart (Example: [1, 2, 3, 4]).
 
 * ``key`` - This is the key of the data series you are trying to forecast (for forecasting revenues use key 'revenue').
 
 * ``settings`` - Has 2 options: 'chartHidden' or left blank.
 
  #. 'chartHidden' is for hiding values from being displayed in the chart. This is useful when we need to forecast rates and ratios, that are too small to be displayed on the chart.
  
  #. Leave blank if you want to display the forecasted list to the chart.

Returns the list with any user edits. For example, if we forecast list [1, 2, 3, 4] and the user changes index [1] value (current value is 2) to 5, then the function will return list [1, 5, 3, 4].

Example:

.. code-block:: javascript

   $.when(
     get_income_statement()).done(
     function(_income){
       var income = deepCopy(_income);
       // Fill the chart with the revenues in the last 10 years of income statements, formatted to millions
       fillHistoricUsingReport(income.slice(0,10), 'revenue', 'M');

       // We will build a revenue forecast based on the last annual revenue reported in the income statement
       // We also need to convert the value to millions toM(), because the forecast function does not support number formatting
       var lastRevenue = toM(income[0].revenue);

       // To make a forecast example, we will assume the revenue grows 5% each year for 3 years
       var forecastedRevenue = [lastRevenue * 1.05,
                                lastRevenue * Math.pow(1.05, 2),
                                lastRevenue * Math.pow(1.05, 3)];
       var forecastedRevenue = forecast(forecastedRevenue, 'revenue');
       renderChart('Revenues chart');
   });

``renderChart()`` function:
***************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L750>`__

Some technical explanation for when the rendering happens: 

 In  `valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions.js>`__ there is a global object that stores all chart data called ``_chart_data`` which has the following members:
  * ``x_historic`` - This is the X-Axis for the historic data, where we usually store years (for example 2012 - 2022, a historic period of 10 years).
  * ``x_forecasted`` - This is the X-Axis for the forecasted data (for example 2022 - 2032, a future period of 10 years).
  * ``y_historic`` - This is the Y-Axis for historic data, where we store historic revenue, net income, etc.
  * ``y_forecasted`` - This is the Y-Axis for future data (future revenue, net income, etc.).
  * ``y_forecasted_chart_hidden`` - This is not actually visible on the chart. It is a part of the forecast table. Please, refer to the ``forecast()`` section.
  * ``name`` - This is the title of the chart.
  * ``hidden_series`` - This controls the visibility of individual data series (when we hide revenues for example, it will be stored here).

Displaying a Table
-------------------

Tables use row headers as keys (revenue, net income, etc.) and column headers as years (2022, 2023, etc.) and all other cells are actual values.

The following example renders a chart with historic revenues and net income:

.. code-block:: javascript

   $.when(
     get_income_statement()).done(
     function(_income){
       var income = deepCopy(_income);
       // Add 2 rows
       var rows = ['Revenues', 'Net Income'];
       // Build a list of years
       var columns = getYear(reportKeyToList(income, 'date'));
       // Add the table data
       var data = [reportKeyToList(income, 'revenue', 'M'), reportKeyToList(income, 'netIncome', 'M')];
       // Add the chart to the context
       renderTable('Full history of data', data, rows, columns);
   });

.. warning::

 The table functions are under active development and may change in the future.

``reportKeyToList()`` function:
*******************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L261>`__

Adds rows to the table from a report retrieved from the API. It then returns a list of values from the report provided.

Arguments of ``reportKeyToList(report, key, measure)``

 * ``report`` - The report object from the API. For example: income statement.
 
 * ``key`` - This is the historic data series key that you'll want to fill the table with (for historic revenues use key 'revenue')
 
 * ``measure`` - Has 3 options: 'M', 'K' or left blank.
 
.. code-block:: javascript
   $.when(
     get_income_statement()).done(
     function(_income){
       var income = deepCopy(_income);
       print(reportKeyToList(income.slice(0,5), 'revenue', 'M'), 'List of revenues');
   });
   
   >>> List of revenues: 394328,365817,274515,260174,265595 
 
``renderTable()`` function:
***************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L814>`__

Renders the table to the screen, similar to the ``renderChart()`` function.
 
Arguments of ``renderTable(name, data, rows, columns)``

 * ``name`` - The table's name. Example: 'My Table'
 
 * ``data`` - The values for all cells. This must be an array of size: **rows.length x columns.length**
 
 * ``rows`` - The row headers. Example ['Revenues', 'Operating Costs', 'Net Income']
 
 * ``columns`` - The column headers. Usually the dates/years for the data.


Dates functions
---------------

``getYear()`` function:
************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L324>`__

Arguments of ``getYear(date)``

 * ``date`` - The full date in %YY-%mm-%dd format or 2022-12-31

.. code-block:: javascript

   print(getYear('2022-12-31'));
   print(getYear(['2022-12-31', '2021-12-31', '2020-12-31']));
   
   >>> 2022
   >>> 2022,2021,2020
   

Utility functions
------------------

``fxRate()`` function:
****************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L211>`__

Retrieves the FX Rate of conversion between 2 currencies.

Arguments of ``fxRate(fx, fromCurrency, toCurrency)``

 * ``fx`` - The report object from the API. For example: income statement.
 
 * ``fromCurrency`` - This is the historic data series key that you'll want to fill the table with (for historic revenues use key 'revenue')
 
 * ``toCurrency`` - Has 3 options: 'M', 'K' or left blank.

.. code-block:: javascript

   $.when(
     get_fx()).done(
     function(_fx){
       var fx = deepCopy(_fx);
       var rate = fxRate(fx,  'USD', 'EUR');
       print(rate, 'FX Rate');
   });
   
   >>> FX Rate: 0.9766 

``newArrayFill()`` function:
****************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L277>`__

Returns a new array with a specified length of the same object.
 
Arguments of ``newArrayFill(length, fillObject)``

 * ``length`` - The length of the new array
 
 * ``fillObject`` - The object the array will be filled with. Could be a number, a string or an object.
 
.. code-block:: javascript

 // Array filled of length 10 filled with zeros
 var testArray = newArrayFill(10, 0);
 print(testArray, 'Test Array');
 
 >>> Test Array: 0,0,0,0,0,0,0,0,0,0

``arrayValuesToRates()`` function:
**********************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L285>`__

Converts an array of values to an array of rate strings. For example, 0.1 is converted to '10%'

.. code-block:: javascript

 // Make a new array of values of length 3 and 0.5 values
 var valuesArray = newArrayFill(3, 0.5);
 // Convert to rates, these are string format, do not use as numbers
 var ratesArray = arrayValuesToRates(valuesArray);
 print(ratesArray, 'Rates Array');
 
 >>> Rates Array: 50%,50%,50% 

``getArraySum()`` function:
***************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L293>`__

Get the sum of all elements in an array of numbers.

.. code-block:: javascript

 // Make a new array of values
 var valuesArray = [1, 2, 3, 4];
 // Get the sum of all elements in the array
 var sum = getArraySum(valuesArray);
 print(sum, 'Sum of all elements');

 >>> Sum of all elements: 10 

``getGrowthRateList()`` function:
*********************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L303>`__

Returns an array of growth rates based on a given input array of values.

Arguments of ``getGrowthRateList(values, mode)``

 * ``values`` - The array of values.
 
 * ``mode`` - Has 2 options: 'percentage' or left blank
 
  #. 'percentage' - will return rate strings
  
  #. Leave blank - will return numbers
  
.. code-block:: javascript

 // Dividend Growth Rates
 var dividends = [0.5, 0.6, 0.7, 0.8, 0.9, 1];
 var dividendGrowth = getGrowthRateList(dividends, 'percentage');
 print(dividendGrowth, 'Dividend Growth Rates (as %)');
 var dividendGrowth = getGrowthRateList(dividends);
 print(dividendGrowth, 'Dividend Growth Rates');
 
 >>> Dividend Growth Rates (as %): ,20.00%,16.67%,14.29%,12.50%,11.11% 
 >>> Dividend Growth Rates: 0,0.19999999999999996,0.16666666666666663,0.142857142857143,0.12499999999999997,0.11111111111111108 

``addKey()`` function:
**********************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L189>`__

Add a data series from one report to another. Add revenues (which is located in the income statements) to all cash flow statements.

.. code-block:: javascript

   $.when(
      get_income_statement(),
      get_cash_flow_statement()).done(
      function(_income, _flows){
        var income = deepCopy(_income);
        var flows = deepCopy(_flows);

        // Add the revenue key to the flows report
        flows = addKey('revenue', income, flows);

        // Press F12 or right-click to inspect console output
        console.log(flows);
    });

``linearRegressionGrowthRate()`` function:
******************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L159>`__

Create a linear regression array from a report. 

For example, create a regression line for historic revenues, present in the income statement.

Arguments of ``linearRegressionGrowthRate(report, key, projection_years, slope)``:

 * ``report`` - The report which contains the data series.
 
 * ``key`` - The key of the data series (For example 'revenue').
 
 * ``projection_years`` - The number of years the regression line will project into the future.
 
 * ``slope`` - The level of inclination of the regression line. <0 for inverse inclination, 0 for flat, 1 for normal, >1 for steeper curve.

.. code-block:: javascript

 $.when(
   get_income_statement()).done(
   function(_income){
     var income = deepCopy(_income);

     var projection_years = 5;
     var slope_value = 1;

     var linRevenue = linearRegressionGrowthRate(income, 'revenue', projection_years, slope_value);
     for(var i in linRevenue){
       linRevenue[i] = toM(linRevenue[i]);
     }
     fillHistoricUsingReport(income, 'revenue', 'M');
     fillHistoricUsingList(linRevenue, 'regressionRevenue');
     renderChart('Revenues');
 });

``averageMargin()`` function:
*****************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L136>`__

Calculates the historic average of one data series (key1) divided by another data series (key2) from the provided report.

Arguments of ``averageMargin(key1, key2, report)``:

 * ``key1`` - The key of the data series number 1 (For example 'netIncome')
 
 * ``key2`` - The key of the data series number 2 (For example 'revenue')
 
 * ``report`` - The report retrieved from the API that contains the two keys.

.. code-block:: javascript

   $.when(
     get_income_statement()).done(
     function(_income){
       var income = deepCopy(_income);

       var averageNetIncomeMargin = averageMargin('netIncome', 'revenue', income);
       print(averageNetIncomeMargin, 'Average Net Income Margin', '%');
   });
   
   >>> Average Net Income Margin: 11.45% 

``averageGrowthRate()`` function:
*********************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L115>`__

Calculates the average growth rate of all growth rates of a data series from a given report.

Arguments of ``averageGrowthRate(key, report)``:

 * ``key`` - The key of the data series (For example 'revenue')
 
 * ``report`` - The report retrieved from the API that contains the data series.

.. code-block:: javascript

   $.when(
     get_income_statement()).done(
     function(_income){
       var income = deepCopy(_income);
      // Average Revenue Growth Rate
       print(averageGrowthRate('revenue', income), 'Average Revenue Growth Rate', '%');
   });
   
   >>> Average Revenue Growth Rate: 18.00% 

``applyMarginToList()`` function:
*********************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L108>`__

Multiplies all elements of a list by a given margin.

Arguments of ``applyMarginToList(list, margin)``:

 * ``list`` - The list of values.
 
 * ``margin`` - The margin you want to apply.

.. code-block:: javascript

   var listOfNumbers = [1, 2, 3, 4, 5];
   print(applyMarginToList(listOfNumbers, 0.5), 'Margin of list');
   
   >>> Margin of list: 0.5,1,1.5,2,2.5 

``getGrowthList()`` function:
*****************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L93>`__

Calculates future values of a data series from a given report based on a given rate.

Arguments of ``getGrowthList(report, key, length, rate)``:

 * ``report`` - The report that contains the data series.
 
 * ``key`` - The key of the data series you want to grow.

 * ``length`` - The number of projected years.
 
 * ``rate`` - The rate at which you project growth.
 
.. code-block:: javascript
 
   $.when(
     get_income_statement()).done(
     function(_income){
       var income = deepCopy(_income);
       var growthYears = 3;
       var growthRate = 0.1;  // 10%

       print(income[0].revenue, 'Last revenue')
      // Average Revenue Growth Rate
       print(getGrowthList(income[0], 'revenue', growthYears, growthRate), 'List of future revenues');
   });
   
   >>> Last revenue: 394328000000 
   >>> List of future revenues: 433760800000.00006,477136880000.00006,524850568000.0002 

``toM()`` function:
*******************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L37>`__

Formats the given number to millions. 

Basically, it divides the input by 1,000,000.

.. code-block:: javascript

   var num = 123456789000;
   print(toM(num), 'toM(number)');
   print(toM([num, num*2, num*3]), 'toM(array)');

   >>> toM(number): 123456.789 
   >>> toM(array): 123456.789,246913.578,370370.367 

``toK()`` function:
*******************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L49>`__

Formats the given number or array to thousands. 

Basically, it divides the input by 1,000.

.. code-block:: javascript

   var num = 123456789;
   print(toK(num), 'toK(number)');
   print(toK([num, num*2, num*3]), 'toK(array)');
   
   >>> toK(number): 123456.789 
   >>> toK(array): 123456.789,246913.578,370370.367 
   
``toR()`` and ``toN()`` functions:
*********************************

``toR()`` formats a given number or array of numbers to a rate or an array of rates. `Source toR() <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L61>`__

``toN()`` formats a given rate or array of rates to a number or an array of numbers. 
`Source toN() <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L77>`__

Basically, ``toR()`` multiplies the input by 100 and ``toN()`` divides the input by 100.

.. note::

   The functions also have long forms and can be used interchangeably:

    * The long form for ``toR()`` is ``numberToRate()``

    * The long form for ``toN()`` is ``rateToNumber()``

.. code-block:: javascript

   var number = 0.5;
   var array = [0.1, 0.055, 0.12345];

   print(toR(number), 'toR(number)');
   print(toR(array), 'toR(array)');

   print(toN(number), 'toN(number)');
   print(toN(array), 'toN(array)');

   >>> toR(number): 50 
   >>> toR(array): 10,5.5,12.345 
   
   >>> toN(number): 0.005 
   >>> toN(array): 0.1,0.055,0.12345 
