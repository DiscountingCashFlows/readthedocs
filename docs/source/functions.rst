Valuation functions
====================

The following functions are found in `DiscountingCashFlows/Documentation/source-code/valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions.js>`__ with the exception of ``$.when().done()`` function. We will cover each function and its use cases.

.. note::

   Please note that this section is currently under active development. Some functions may not be up to date. If you find any outdated functions, please let us know `here <https://discountingcashflows.com/help/>`__

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

Prints values and messages to the screen. Below are examples of usage types:

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

The flow of creating a chart is:

 #. Step 1. Fill the historic data (for example the company's historic revenues, net income, etc.) by using either ``fillHistoricUsingReport()`` or ``fillHistoricUsingList()``, whichever you find easier to setup.
 
 #. Step 2. Fill the forecasted data by using ``forecast()``. This data represents the 'assumptions' for the chart (for example predicting future revenues).
 
 #. Step 3. Be sure to render the chart or else it won't be shown on the screen.

``fillHistoricUsingReport()`` function:
***************************************

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
 
 // Adds the full history of eps from the income statements
 fillHistoricUsingReport(income, 'eps');
 
 // Adds the revenues, formatted to millions, of the last 10 years of income statements
 fillHistoricUsingReport(income.slice(0,10), 'revenue', 'M');
 
``fillHistoricUsingList()`` function:
***************************************

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
 
Example without ``endingYear``:
 
.. code-block:: javascript

 // The ending year will be the report's ending year.
 fillHistoricUsingReport(income.slice(0,10), 'revenue', 'M');
 fillHistoricUsingList([1, 2, 3, 4], 'myList');
 
``forecast()`` function:
************************

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

Tables use row headers as keys (revenue, net income, etc.) and column headers as years (2022, 2023, etc.) and all other cells are actual values.

The following example renders a chart with historic revenues and net income:

.. code-block::

 // The context array is used to hold table data
 var context = [];
 // Add 2 rows
 var rows = ['Revenues', 'Net Income'];
 lastYearDate = parseInt(income[0]['date']);
 // Add columns, starting with the income reports first year and ending with the last year
 var columns = [];
 for(var i=1; i <= income.length; i++){
   columns.push(lastYearDate - i);
 }
 // Add the table data
 var data = [toList(income, 'revenue', 'M'), toList(income, 'netIncome', 'M')];
 // Add the chart to the context
 context.push({name:'Full history of data', display:'table', rows:rows, columns:columns, data:data});
 // Render the chart
 monitor(context);

.. warning::

 The table functions are under active development and may change in the future.

``toList()`` function:
************************

Adds rows to the table from a report retrieved from the API. It then returns a list of values from the report provided.

Arguments of ``toList(report, key, measure)``

 * ``report`` - The report object from the API. For example: income statement.
 
 * ``key`` - This is the historic data series key that you'll want to fill the table with (for historic revenues use key 'revenue')
 
 * ``measure`` - Has 3 options: 'M', 'K' or left blank.
 
.. code-block::
   $.when(
     get_income_statement()).done(
     function(_income){
       var income = JSON.parse(JSON.stringify(_income));
       print(toList(income.slice(0,5), 'revenue', 'M'), 'List of revenues');
   });
   
   >>> List of revenues: 394328,365817,274515,260174,265595 
 
``monitor()`` function:
***********************

Renders the table to the screen, similar to the ``renderChart()`` function, but it also has a little more functionality.

.. note::

 The ``monitor()`` function is also used by the ``print()`` function to render prints, but we don't recommend printing using the ``monitor()`` function. Print example: ``{name:'Revenue', display:'value', data:1,000,000,000, currency: 'USD'}`` -> Will display ``Revenue: 1,000,000,000 USD``

Utility functions:
-------------------

``fxRate()`` function:
****************************

Retrieves the FX Rate of conversion between 2 currencies.

Arguments of ``fxRate(fx, fromCurrency, toCurrency)``

 * ``fx`` - The report object from the API. For example: income statement.
 
 * ``fromCurrency`` - This is the historic data series key that you'll want to fill the table with (for historic revenues use key 'revenue')
 
 * ``toCurrency`` - Has 3 options: 'M', 'K' or left blank.

.. code-block::

 $.when(
   get_fx()).done(
   function(_fx){
     var fx = JSON.parse(JSON.stringify(_fx));
     var fxRate = fxRate(fx,  'USD', 'EUR');
     print(fxRate, 'FX Rate');
   });
   
   >>> FX Rate: 0.9766 

``newArrayFill()`` function:
****************************

Returns a new array with a specified length of the same object.
 
Arguments of ``newArrayFill(length, fillObject)``

 * ``length`` - The length of the new array
 
 * ``fillObject`` - The object the array will be filled with. Could be a number, a string or an object.
 
.. code-block::

 // Array filled of length 10 filled with zeros
 var testArray = newArrayFill(10, 0);
 print(testArray, 'Test Array');
 
 >>> Test Array: 0,0,0,0,0,0,0,0,0,0

``arrayValuesToRates()`` function:
**********************************

Converts an array of values to an array of rate strings. For example, 0.1 is converted to '10%'

.. code-block::

 // Make a new array of values of length 3 and 0.5 values
 var valuesArray = newArrayFill(3, 0.5);
 // Convert to rates, these are string format, do not use as numbers
 var ratesArray = arrayValuesToRates(valuesArray);
 print(ratesArray, 'Rates Array');
 
 >>> Rates Array: 50%,50%,50% 

``getArraySum()`` function:
***************************

Get the sum of all elements in an array of numbers.

.. code-block::

 // Make a new array of values
 var valuesArray = [1, 2, 3, 4];
 // Get the sum of all elements in the array
 var sum = getArraySum(valuesArray);
 print(sum, 'Sum of all elements');

 >>> Sum of all elements: 10 

``getGrowthRateList()`` function:
*********************************

Returns an array of growth rates based on a given input array of values.

Arguments of ``getGrowthRateList(values, mode)``

 * ``values`` - The array of values.
 
 * ``mode`` - Has 2 options: 'percentage' or left blank
 
  #. 'percentage' - will return rate strings
  
  #. Leave blank - will return numbers
  
.. code-block::

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

Add a data series from one report to another. Add revenues (which is located in the income statements) to all cash flow statements.

.. code-block::

 $.when(
   get_income_statement(),
   get_cash_flow_statement()).done(
   function(_income, _flows){
     var income = JSON.parse(JSON.stringify(_income));
     var flows = JSON.parse(JSON.stringify(_flows));

     income = income[0];
     flows = flows[0];

     // Add the revenue key to the flows report
     flows = addKey('revenue', income, flows);

     // Press F12 or right-click to inspect console output
     console.log(flows);
 });

``linearRegressionGrowthRate()`` function:
******************************************

Create a linear regression array from a report. 

For example, create a regression line for historic revenues, present in the income statement.

Arguments of ``linearRegressionGrowthRate(key, report, years, slope)``:

 * ``key`` - The key of the data series (For example 'revenue')
 
 * ``report`` - The report which contains the data series
 
 * ``years`` - The number of years the regression line will continue into the future
 
 * ``slope`` - The level of inclination of the regression line. <0 for inverse inclination, 0 for flat, 1 for normal, >1 for steeper curve

.. code-block::

 $.when(
   get_income_statement()).done(
   function(_income){
     var income = JSON.parse(JSON.stringify(_income));

     var projection_years = 5;
     var slope_value = 1;

     var linRevenue = linearRegressionGrowthRate('revenue', income, projection_years, slope_value);
     for(var i in linRevenue){
       linRevenue[i] = toM(linRevenue[i]);
     }
     fillHistoricUsingReport(income, 'revenue', 'M');
     fillHistoricUsingList(linRevenue, 'regressionRevenue');
     renderChart('Revenues');
 });

``averageMargin()`` function:
*****************************

Calculates the historic average of one data series (key1) divided by another data series (key2) from the provided report.

Arguments of ``averageMargin(key1, key2, report)``:

 * ``key1`` - The key of the data series number 1 (For example 'netIncome')
 
 * ``key2`` - The key of the data series number 2 (For example 'revenue')
 
 * ``report`` - The report retrieved from the API that contains the two keys.

.. code-block::

   $.when(
     get_income_statement()).done(
     function(_income){
       var income = JSON.parse(JSON.stringify(_income));

       var averageNetIncomeMargin = averageMargin('netIncome', 'revenue', income);
       print(averageNetIncomeMargin, 'Average Net Income Margin', '%');
   });
   
   >>> Average Net Income Margin: 11.45% 

``averageGrowthRate()`` function:
*********************************

Calculates the average growth rate of all growth rates of a data series from a given report.

Arguments of ``averageGrowthRate(key, report)``:

 * ``key`` - The key of the data series (For example 'revenue')
 
 * ``report`` - The report retrieved from the API that contains the data series.

.. code-block::

   $.when(
     get_income_statement()).done(
     function(_income){
       var income = JSON.parse(JSON.stringify(_income));
      // Average Revenue Growth Rate
       print(averageGrowthRate('revenue', income), 'Average Revenue Growth Rate', '%');
   });
   
   >>> Average Revenue Growth Rate: 18.00% 

``applyMarginToList()`` function:
*********************************

Multiplies all elements of a list by a given margin.

Arguments of ``applyMarginToList(list, margin)``:

 * ``list`` - The list of values.
 
 * ``margin`` - The margin you want to apply.

.. code-block::

   var listOfNumbers = [1, 2, 3, 4, 5];
   print(applyMarginToList(listOfNumbers, 0.5), 'Margin of list');
   
   >>> Margin of list: 0.5,1,1.5,2,2.5 

``getGrowthList()`` function:
*****************************

Calculates future values of a data series from a given report based on a given rate.

Arguments of ``getGrowthList(report, key, length, rate)``:

 * ``report`` - The report that contains the data series.
 
 * ``key`` - The key of the data series you want to grow

 * ``length`` - The number of projected years
 
 * ``rate`` - The rate at which you project growth
 
.. code-block::
 
   $.when(
     get_income_statement()).done(
     function(_income){
       var income = JSON.parse(JSON.stringify(_income));
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

Formats the given number to millions. Basically it divides the input by 1,000,000. For example, 123,456,789,000 is formatted to 123,456.789

.. code-block::

 print(toM(123456789000), 'toM()');
 >>> toM(): 123456.789 

``toK()`` function:
*******************

Formats the given number to thousands. Basically it divides the input by 1,000. For example, 123,456,789 is formatted to 123,456.789

.. code-block::

 print(toK(123456789), 'toK()');
 >>> toK(): 123456.789 

``Description()`` function:
***************************

The ``Description()`` function serves as a quick readme for the model and it is shown at the top of each model. It supports html formatting, so you can style it any way you want.

.. code-block:: javascript

 Description(`<h5>Base Model Code</h5>
             <p>This is the base code for writing valuation models.</p>
             <p class='text-center'>Read more: <a href='https://github.com/DiscountingCashFlows/Documentation/' target='_blank'><i class="fab fa-github"></i> GitHub Documentation</a></p>
             `);
