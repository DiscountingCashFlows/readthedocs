Valuation functions
====================

This is the official documentation for the Valuation Framework of Discounting Cash Flows Website (discountingcashflows.com)

.. warning::

   All code needs to be compatible with `EcmaScript 5.1 (ES5.1) <https://www.w3schools.com/js/js_es5.asp>`__
   Features from ES6 do not work and need to be avoided.
   
Github repository source code: `valuation-functions/new-valuation-functions.js <https://github.com/DiscountingCashFlows/Documentation/blob/main/source-code/valuation-functions/new-valuation-functions.js>`__. 

.. note::

   Please note that this section is currently under active development. Some functions may not be up to date. If you find any outdated functions, please let us know `here <https://discountingcashflows.com/help/>`__

$.when().done() - Function
------------------------

The ``$.when().done()`` function is an asynchronous function from the popular JavaScript Framework 'jQuery' that retrieves the financial data for us. The ``$.when().done()`` function is the only function that we will use from the 'jQuery' framework in our valuations.

To understand the ``$.when().done()`` function, we need to think about the workflow of valuing a company. 
 
Step 1. Gather financial data for the company (get the income statements, the balance sheets, the cashflow reports, dividends, etc. )

Step 2. Process the data via a valuation model to calculate an intrinsic value of the company.

The ``$.when().done()`` function takes care of Step 1. It retrieves the financial data from our :doc:`api` just like in the following example:

.. code-block:: javascript

   Input(
     {
       // Assumptions go here
     }
   );

   $.when(
     get_income_statement(),
     get_income_statement_ltm(),
     get_balance_sheet_statement(),
     get_balance_sheet_statement_quarterly(),
     get_cash_flow_statement(),
     get_cash_flow_statement_ltm(),
     get_profile(),
     get_dividends_annual(),
     get_treasury(),
     get_fx(),
     get_risk_premium()).done(
     function($income, $income_ltm, $balance, $balance_quarterly, $flows, $flows_ltm, $profile, $dividends, $treasury, $fx, $risk_premium){
     try{

       // Here we take care of Step 2
       // Process the data to calculate an intrinsic value of the company...

     }
     catch (error) {
      throwError(error);
     }
   });


So, why do we use ``$.when().done()`` anyway and not some other function like ``$.get()``? 

The advantage of using ``$.when().done()`` is that we submit all requests at once, instead of one by one.

You can think about it like going to the grocery:

 Method 1: You can go to the grocery, purchase item1, return home, go back to the grocery, purchase item2, return home, go back to the grocery store, purchase the last item3 and return home and then start cooking.
 
 Method 2: You can also (and probably should) go to the grocery and purchase item1, item2 and item3 simultaneously and then return home with all the groceries and start cooking.
 
So, you'd probably want to stick to method 2 because it saves a lot of time. And that's exactly what ``$.when().done()`` does. It retrieves all data at once and once it is ``done()``, it can begin processing the data.


Response() - Class
------------------

The ``Response()`` class is used to unpack financial data.

Once all data has been retrieved from the API, it is now in "http response" format and it would look like something this:

.. code-block:: javascript

   _income = {
      0: Array(...) [ {…}, {…}, {…}, … ]
      1: "success"
      2: Object { readyState: 4, getResponseHeader: getResponseHeader(e), getAllResponseHeaders: getAllResponseHeaders(), … }
      length: 3
   }

The "http response" format is not very pretty and we should **never** use the object in "http response" format directly as it can cause caching issues in the Watchlist & Notifications page.

We should always use the  ``Response()`` class to unpack the financial data. By using this class we make sure that:
   1. We avoid any caching issues.
   2. All financial data values are stored in one currency only.

Example Usage
*************

.. code-block:: javascript
   
   var response = new Response({
     income: $income,
     income_ltm: $income_ltm,
     balance: $balance,
     balance_quarterly: $balance_quarterly,
     balance_ltm: 'balance_quarterly:0',
     flows: $flows,
     flows_ltm: $flows_ltm,
     profile: $profile,
     treasury: $treasury,
     risk_premium: $risk_premium,
   }).toOneCurrency('income', $fx).merge('_ltm');
   // For the balance sheet, we need to set the LTM manually
   response.balance_ltm['date'] = 'LTM';

   print(response.income[0].date, 'Last Date');

Response() - Class Constructor
****************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L30>`__

All data that has been retrieved from the API in "http response" format, needs to be passed into a ``Response()`` object as such:

.. code-block:: javascript

   var response = new Response({
     income: $income,
     profile: $profile,
   }).toOneCurrency('income', $fx);

   print(response.profile.companyName, "Company's Full Name");

   >>> Company's Full Name: Apple Inc.

Response.toOneCurrency() - Class Function
***********************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L92>`__

The function makes sure that all response objects that have been passed in ``Response()`` are valued in one currency, and that currency is the currency of the specified report (through ``report_key``).

If any other passed response object report is in another currency, all values in that report will be converted to the baseline currency at the fx rate passed in ``fx``.

Arguments of ``Response.toOneCurrency(report_key, fx)``

 * ``report_key`` - The name of whichever report you prefer to use as the baseline currency (usually income, balance or flows).
 
 * ``fx`` - The foreign exchange data response object generated by the ``get_fx()`` function. It should contain all currency pairs and their respective rates.

In the example below, ``toOneCurrency('income', _fx)`` uses the currency found in report 'income' as a baseline.

.. code-block:: javascript

   var response = new Response({
     income: $income,
     profile: $profile,
   }).toOneCurrency('income', $fx);

   print(response.profile.companyName, "Company's Full Name");

   >>> Company's Full Name: Apple Inc.

Response.merge() - Class Function
***************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L172>`__

The function merges all keys with an ``extension``, into their respective parents.

For the example below, response.merge('_ltm') merges 'income_ltm' into 'income' and 'flows_ltm' into 'flows'.
 
.. code-block:: javascript

   var response = new Response({
     income: $income,
     income_ltm: $income_ltm,
   }).toOneCurrency('income', $fx).merge('_ltm');

   print(response.income[0].date, 'Last Date');

   >>> Last Date: LTM   

Arguments of ``Response.merge(extension)``

 * ``extension`` - Example response.merge('_ltm') merges 'x_ltm' into 'x', meaning that 'x' will now contain the 'ltm' report and its length would be increased by 1.

DateValueData() - Class
-----------------------

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L192>`__

Now that we have our financial data unpacked in the ``Response()`` object, it is ready to use in our calculations.

Calculations revolve around formulas. Let's take some examples.

 * Example 1: To calculate the Net Margin in year 2022, you would take the Net Income in 2022 and divide it by the Revenue in 2022.

 * Example 2: To calculate the Return on Equity in 2022, you would take the Net Income in 2022 and divide it by the Total Equity in 2021.

If we want to calculate the Net Margin and Return on Equity for multiple years (say 2010 - 2022), we need to generalize the formulas.

 * Example 1: To calculate the Net Margin in year X, take the Net Income in year X and divide it by the Revenue in year X.

 * Example 2: To calculate the Return on Equity in year X, take the Net Income in year X and divide it by the Total Equity in year X - 1 (the previous year).

Also, the class is called DateValueData because it stores pairs of Dates and Values in the following format:

.. code-block:: javascript

   // netIncome contains values for 29 years + LTM
   netIncome: {
      list: (30) [
         0: {
            value: 94321000000
            year: "LTM"
         },
         1: {
            value: 99803000000
            year: 2022
         },
         2: {
            value: 94680000000
            year: 2021
         },
         3: ...
      ]
   }

Example Usage
*************

.. code-block:: javascript
   
   // Required assumptions
   Input(
     {
       HISTORICAL_YEARS: 10,
       FORECAST_YEARS: 5,
       _DISCOUNT_RATE: 10
     }
   );
   
   // Below is the full processing of data (computing historical ratios and forecasting values)
   // Store original data
   var original_data = new DateValueData({
     revenue: new DateValueList(response.income, 'revenue'),
     netIncome: new DateValueList(response.income, 'netIncome'),
     eps: new DateValueList(response.income, 'eps'),
     totalStockholdersEquity: new DateValueList(response.balance, 'totalStockholdersEquity'),
     freeCashFlow: new DateValueList(response.flows, 'freeCashFlow'),
     _treasuryYield: new DateValueList(response.treasury, 'year10', '%'),
     weightedAverageShsOut: new DateValueList(response.income, 'weightedAverageShsOut'),
   });

   // Compute historical ratios and margins
   var historical_computed_data = original_data.setFormula({
     _netMargin: ['netIncome:0', '/', 'revenue:0'],
     _returnOnEquity: ['netIncome:0', '/', 'totalStockholdersEquity:-1'],
     _revenueGrowthRate: ['function:growth_rate', 'revenue'],
     _freeCashFlowMargin: ['freeCashFlow:0', '/', 'revenue:0'],
     discountedFreeCashFlow: ['freeCashFlow'],
   }).compute();

   // Required to know when to start averaging for average_revenue_growth and average_fcf_margin
   var start_date = original_data.lastDate() - getAssumption('HISTORICAL_YEARS');

   // Calculate the average Revenue growth rate beginning 10 years ago (HISTORICAL_YEARS)
   var average_revenue_growth = historical_computed_data.get('_revenueGrowthRate').sublist(start_date).average();
   // Calculate the average Free Cash Flow Margin beginning 10 years ago (HISTORICAL_YEARS)
   var average_fcf_margin = historical_computed_data.get('_freeCashFlowMargin').sublist(start_date).average();

   // Required to know when the forecasting ends
   var forecast_end_date = original_data.lastDate() + getAssumption('FORECAST_YEARS');
   var next_year_date = original_data.lastDate() + 1;
   // Forecast future values
   var forecasted_data = historical_computed_data.setFormula({
     revenue: ['revenue:-1', '*', 1 + average_revenue_growth],
     freeCashFlow: ['revenue:0', '*', average_fcf_margin],
     discountedFreeCashFlow: ['function:discount', 'freeCashFlow', {rate: getAssumption('_DISCOUNT_RATE'), start_date: next_year_date}],
     _freeCashFlowMargin: ['freeCashFlow:0', '/', 'revenue:0'],
     _revenueGrowthRate: ['function:growth_rate', 'revenue'],
   }).setEditable(_edit(), {
     start_date: next_year_date,
     keys: ['revenue', 'freeCashFlow'],
   }).compute({forecast_end_date: forecast_end_date}); 

   // The sum of all discounted Free Cash Flow
   var total_discounted_fcf = forecasted_data.get('discountedFreeCashFlow').sublist(next_year_date).sum();
   // Total shares outstanding
   var total_shares_outstanding = original_data.get('weightedAverageShsOut').lastValue();
   // Total discounted Free Cash Flow divided by the number of shares
   var discounted_fcf_per_share = total_discounted_fcf / total_shares_outstanding;
   
   // Sets the value at the top of the model
   if(_StopIfWatch(discounted_fcf_per_share, response.currency)){
     return;
   }
   _SetEstimatedValue(discounted_fcf_per_share, response.currency);

   print(total_discounted_fcf, 'The sum of all discounted Free Cash Flow', '#', response.currency);
   print(discounted_fcf_per_share, 'Total discounted Free Cash Flow per share', '#', response.currency);

Defining Original Data
**********************

So, the first step is to register the original data into a ``DateValueData()``. In our previous examples the original data is: Net Income, Revenue and Total Equity. Let's see how we do that (we need the ``Response()`` object defined previously).

.. code-block:: javascript

   // Store original data
   var original_data = new DateValueData({
     revenue: new DateValueList(response.income, 'revenue'),
     netIncome: new DateValueList(response.income, 'netIncome'),
     eps: new DateValueList(response.income, 'eps'),
     totalStockholdersEquity: new DateValueList(response.balance, 'totalStockholdersEquity'),
     freeCashFlow: new DateValueList(response.flows, 'freeCashFlow'),
     _treasuryYield: new DateValueList(response.treasury, 'year10', '%'),
     weightedAverageShsOut: new DateValueList(response.income, 'weightedAverageShsOut'),
   });

Notice that we use the ``DateValueList`` class to store our data. Basically the ``DateValueData()`` class is just a collection of ``DateValueList()`` objects.

Writing and Processing Formulas
*******************************

Following up on the previous examples, to calculate the Net Margin and the Return on Equity, our code would look something like this:

.. code-block:: javascript

   // Compute historical ratios and margins
   var historical_computed_data = original_data.setFormula({
     _netMargin: ['netIncome:0', '/', 'revenue:0'],
     _returnOnEquity: ['netIncome:0', '/', 'totalStockholdersEquity:-1'],
     _revenueGrowthRate: ['function:growth_rate', 'revenue'],
     _freeCashFlowMargin: ['freeCashFlow', '/', 'revenue'],
     discountedFreeCashFlow: ['freeCashFlow'],
   }).compute();
   
First, we set the formulas on ``original_data`` using the ``DateValueData.setFormula()`` function. After the formulas have been set we call the ``DateValueData.compute()`` function. Formulas are written between [] and, for now, they support a maximum of 3 items.

Let's look at the '_returnOnEquity' formula. Notice it has 3 items:

   * The first item 'netIncome:0' refers to the 'netIncome' registered in our original_data object and the ':0' refers to the current year.
   
   * The second item '/' refers to an operation (division in this case).
   
   * The third item refers to the 'totalStockholdersEquity' registered in our original_data object and the ':-1' refers to the previous year.

*Also, notice that both '_netMargin' and '_returnOnEquity' keys start with an '_' underline, this is because both of them are treated as percentages. So, beggining with an '_' underline will mark the respective key as a percentage.

DateValueData.setFormula() - Class Function
*************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L233>`__

Writes the formula onto a DateValueData object before calculation.

Must be set before the ``compute()`` operation!

Arguments of ``DateValueData.setFormula(new_formula)``:

 * ``new_formula`` - The new formula object to be set.

Constants
*********

Constants are used when we want a single value for all periods. Here is an example of setting the value 123 for all dates:
   
.. code-block:: javascript

   var computed_data = original_data.setFormula({
      // Creates a list of constant 123
      constant: [123],
   }).compute();
   
   
Copying Other Keys
******************

.. code-block:: javascript

   var computed_data = original_data.setFormula({
      // Creates a copy of Net Income
      copyOfNetIncome: ['netIncome'],
      // Equivalent to the previous formula, but with an explicit ":0"
      copyOfNetIncome: ['netIncome:0'],
      // Creates a copy of Net Income shifted one year into the past.
      shiftedCopyOfNetIncome: ['netIncome:-1'],
   }).compute();

Operations
**********

The full list of operations:

 * '+' - Add
 
 * '-' - Subtract
 
 * '/' - Divide
 
 * '*' - Multiply
 
 * '^' - Power

.. code-block:: javascript

   var computed_data = original_data.setFormula({
      // Divides the totalStockholdersEquity in current year by weightedAverageShsOut in current year
      bookValue: ['totalStockholdersEquity', '/', 'weightedAverageShsOut'],
      // Equivalent to the previous formula, but with an explicit ":0"
      bookValue: ['totalStockholdersEquity:0', '/', 'weightedAverageShsOut:0'],
      // netIncome in current year divided by totalStockholdersEquity in previous year
      _returnOnEquity: ['netIncome:0', '/', 'totalStockholdersEquity:-1'],
   }).compute();
 
'function:discount' and 'function:compound' - Formula Functions
*************************************************************

Formula functions are functions that can be used inside ``DateValueData()`` objects formulas.

 * 'function:discount' - discounts a specified key using a given 'rate' and a present date as 'start_date'

 * 'function:compound' - compounds a specified key using a given 'rate' and a present date as 'start_date'

.. code-block:: javascript

   var computed_data = original_data.setFormula({
      // Discounts the 'freeCashFlow' by '_costOfEquity', starting at 'currentDate'
      discountedFreeCashFlow: ['function:discount', 'freeCashFlow', {rate: '_costOfEquity', start_date: currentDate}],  
      // Discounts the 'freeCashFlow' by constant getAssumption('_DISCOUNT_RATE') set in the assumptions, starting at 'currentDate'
      discountedFreeCashFlow: ['function:discount', 'freeCashFlow', {rate: getAssumption('_DISCOUNT_RATE'), start_date: currentDate}],  
      // Discounts the constant 'freeCashFlow' taken at 'start_date' by '_costOfEquity', starting at 'currentDate'
      discountedFreeCashFlow: ['function:discount', 'freeCashFlow:start_date', {rate: '_costOfEquity', start_date: currentDate}],
      // Discounts the constant 'freeCashFlow' taken at 'other_date' by '_costOfEquity', starting at 'currentDate'
      discountedFreeCashFlow: ['function:discount', 'freeCashFlow:other_date', {rate: '_costOfEquity', start_date: currentDate, other_date: otherDate}],
      // Discounts the constant value 1 by '_costOfEquity', starting at 'currentDate'
      discountedOne: ['function:discount', 1, {rate: '_costOfEquity', start_date: currentDate}],
      // Discounts the constant value 1 by constant value 0.1 (or 10%), starting at 'currentDate'
      discountedOne: ['function:discount', 1, {rate: 0.1, start_date: currentDate}],
   }).compute();
   
   // discountedOne = [1, 0.91, 0.83, 0.75, 0.68, 0.62]

'function:growth_rate' - Formula Function
***************************************

'function:growth_rate' - creates a list of growth rates from a specified key

.. code-block:: javascript

   var computed_data = original_data.setFormula({
      // Calculates the growth rate of key revenue
      _revenueGrowthRate: ['function:growth_rate', 'revenue'],
   }).compute();
   
'function:linear_regression' - Formula Function
*********************************************

'function:linear_regression' - creates a linear regression set from a specified key

.. code-block:: javascript

   var computed_data = original_data.setFormula({
      // Calculates the linear regression of key revenue, with slope = 1, starting back in 2013
      linearRegressionRevenue: ['function:linear_regression', 'revenue', {slope: 1, start_date: 2013}],
   }).compute();
      
DateValueData.compute() - Class Function
***********************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L593>`__

Computes the stored formulas that were set using ``DateValueData.setFormula()`` in the correct order.

Arguments of ``DateValueData.compute(properties)``:

 * ``properties`` - (*Optional) Object containing the compute end date.
 
*If ``properties`` is left blank, then the computation will stop at last date in the ``DateValueData`` object. This means that if the ``DateValueData`` object starts at 1990 and ends in 2022, the compute function will compute the formulas for each year between 1990 and 2022.

For forecasting, we need to specify the number of years to continue computing formulas. We achieve this with a ``properties`` object:

.. code-block:: javascript
   
   properties = {
      forecast_years: 5
   }
   
   // Or if we want to specify the date
   
   properties = {
      forecast_end_date: 2027
   }
   
   // Forecasting Example:
   var forecast_end_date = historical_computed_data.lastDate() + getAssumption('FORECAST_YEARS');
   var forecasted_data = historical_computed_data.setFormula({
      ...
   }).compute({'forecast_end_date': forecast_end_date});
 
Full Forecasting Example:

.. code-block:: javascript

   // Required assumptions
   Input(
     {
       HISTORICAL_YEARS: 10,
       FORECAST_YEARS: 5,
       _DISCOUNT_RATE: 10
     }
   );

   // Required to know when to start averaging for average_revenue_growth and average_fcf_margin
   var start_date = original_data.lastDate() - getAssumption('HISTORICAL_YEARS');

   // Calculate the average Revenue growth rate beginning 10 years ago (HISTORICAL_YEARS)
   var average_revenue_growth = historical_computed_data.get('_revenueGrowthRate').sublist(start_date).average();
   // Calculate the average Free Cash Flow Margin beginning 10 years ago (HISTORICAL_YEARS)
   var average_fcf_margin = historical_computed_data.get('_freeCashFlowMargin').sublist(start_date).average();

   // Required to know when the forecasting ends
   var forecast_end_date = original_data.lastDate() + getAssumption('FORECAST_YEARS');
   var next_year_date = original_data.lastDate() + 1;
   
   // Forecast future values
   var forecasted_data = historical_computed_data.setFormula({
     revenue: ['revenue:-1', '*', 1 + average_revenue_growth],
     freeCashFlow: ['revenue:0', '*', average_fcf_margin],
     discountedFreeCashFlow: ['function:discount', 'freeCashFlow', {rate: getAssumption('_DISCOUNT_RATE'), start_date: next_year_date}],
     _freeCashFlowMargin: ['freeCashFlow:0', '/', 'revenue:0'],
     _revenueGrowthRate: ['function:growth_rate', 'revenue'],
   }).setEditable(_edit(), {
     start_date: next_year_date,
     keys: ['revenue', 'freeCashFlow'],
   }).compute({forecast_end_date: forecast_end_date}); 
      
DateValueData.setEditable() - Class Function
***************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L1056>`__

Sets DateValueData keys as editable. They can then be edited from the chart or from the forecast table.

For this to work, make sure to include to render the chart :ref:`render-chart-header-label`.

Must be set before the ``compute()`` operation!

Arguments of ``DateValueData.setEditable(_edit(), object)``:

 * ``_edit()`` - This is required to be always ``_edit()``
 
 * ``object`` - Object that contains the editable keys and the start date
 
   object = {
      start_date: nextYear,
      keys: ['key1', 'key2', ...],
   }

Example:

.. code-block:: javascript
   
   var next_year_date = original_data.lastDate() + 1;
   var forecasted_data = historical_computed_data.setFormula({
     revenue: ...,
     freeCashFlow: ...,
   }).setEditable(_edit(), {
     start_date: next_year_date,
     keys: ['revenue', 'freeCashFlow'],
   }).compute({'forecast_end_date': forecast_end_date});

DateValueData.removeDate() - Class Function
***************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L431>`__

Loops all data and removes all items at the specified date.

Arguments of ``DateValueData.removeDate(date)``

 * ``date`` - The date to be removed from the ``DateValueData`` object.
 
The following example shows how we can remove the Last Twelve Months items from a table:

.. code-block:: javascript
   
   forecasted_data.removeDate('LTM').renderTable({
     ...
   });

DateValueList() - Class
-----------------------

The ``DateValueList()`` class contains a list of date-value pairs. Storing the data in this format will make sure that dates will not get mixed up, when performing calculations.

Below is an example of a ``DateValueList()`` object format:

.. code-block:: javascript

   // netIncome contains values for 29 years + LTM
   netIncome: {
      list: (30) [
         0: {
            value: 94321000000
            year: "LTM"
         },
         1: {
            value: 99803000000
            year: 2022
         },
         2: {
            value: 94680000000
            year: 2021
         },
         3: ...
      ]
   }


DateValueList() - Class Constructor
**************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L1315>`__

``DateValueList()`` objects can be created using one the following methods:

.. code-block:: javascript
   
   // Note: 
   // For this example to work, $income and $treasury need to be retrieved 
   // by using get_income_statement() and get_treasury_annual()
   
   var response = new Response({
      income: $income,
      treasury: $treasury,
      // Other reports ...
   }).toOneCurrency('income', $fx);
   
   // The DateValueList() class is built to be stored inside a DateValueData() object
   var original_data = new DateValueData({
   
      // Example 1: Store the list of revenues from the income statement
      revenue: new DateValueList(response.income, 'revenue'),
      
      // Example 2: Store the list of treasury yields as percentages. 
      // Note the '%' argument at the end will divide all values by 100.
      _treasuryYield: new DateValueList(response.treasury, 'year10', '%'),
      
   });

DateValueList.average() - Class Function
*************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L1417>`__

Gets the average of all values in the ``DateValueList`` object. It is the sum of all values divided by the number of dates available.

``DateValueList.average()`` has no arguments.

Example: 

.. code-block:: javascript
   
   // Note: This example follows the previous example from DateValueList() - Class Constructor
   
   // Get the average revenue for all dates available
   var averageRevenue = original_data.get('revenue').average();
   
   // Get the average revenue starting from 5 years ago ( using .sublist )
   var startDate = original_data.lastDate() - 5;
   var averageRevenue = original_data.get('revenue').sublist(startDate).average();

DateValueList.sum() - Class Function
*********************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L1421>`__

Gets the sum of all values in the ``DateValueList`` object. 

``DateValueList.average()`` has no arguments.

Example: 

.. code-block:: javascript
   
   // Note: This example follows the previous example from DateValueList() - Class Constructor
   
   // Get the sum of all revenues for all dates available
   var sumRevenue = original_data.get('revenue').sum();
   
   // Get the sum of all revenues starting from 5 years ago ( using .sublist )
   var startDate = original_data.lastDate() - 5;
   var sumRevenue = original_data.get('revenue').sublist(startDate).sum();

DateValueList.sublist() - Class Function
*************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L1441>`__

Gets a sublist of the ``DateValueList`` object by providing a start date and optionally an end date.

Arguments of ``DateValueList.sublist(start_date, end_date)``

 * ``start_date`` - The start date for the sublist.
 
 * ``end_date`` - (Optional) End date. If the end date is not provided, then the last date in the ``DateValueList`` object will be considered end date.
 
Example: 

.. code-block:: javascript
   
   // Note: This example follows the previous example from DateValueList() - Class Constructor
   
   // startDate is the date from 5 years ago.
   var startDate = original_data.lastDate() - 5;
   // Get 5 years of revenues
   var sublistRevenue = original_data.get('revenue').sublist(startDate);
   
   /*
      Providing an endDate (additionally to the startDate) will 
      retrieve a DateValueList sublist from the startDate to endDate.
      The endDate is the date from 1 year ago.
   */
   var endDate = original_data.lastDate() - 1;
   var sublistRevenue = original_data.get('revenue').sublist(startDate, endDate);

DateValueList.valueAtDate() - Class Functions
*****************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L1378>`__

Get the value at a specified Date from a ``DateValueList`` object. If the date is not found, the function returns null.

Arguments of ``DateValueList.valueAtDate(date)``

 * ``date`` - The date to search for in the list. 

Example:

.. code-block:: javascript
   
   // Note: This example follows the previous example from DateValueList() - Class Constructor
   
   // Retrieve the Last Twelve Months 10Y treasury yield
   var ltmTreasuryYield = original_data.get('_treasuryYield').valueAtDate('LTM');
   
   // Retrieve the 10Y treasury yield from 5 years ago
   var historicalTreasuryYield = original_data.get('_treasuryYield').valueAtDate(original_data.lastDate() - 5);
   

DateValueList.lastValue() - Class Functions
****************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/82196c4db3d381c3eb44f2aeed8daeef677ecb15/source-code/valuation-functions/new-valuation-functions.js#L1512>`__

Returns the value from the Last Twelve Months(LTM) if it exists or highest year if LTM is not found.

``DateValueList.lastValue()`` takes no arguments.

Example:

.. code-block:: javascript

   // Retrieve the latest 10Y treasury yield, LTM value if exists or latest year
   var lastTreasuryYield = original_data.get('_treasuryYield').lastValue();
 
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
 
``throwWarning()`` and ``warning()`` functions:
***********************
 
Display a warning alert message (in yellow) on the top of the model.
 
.. code-block:: javascript

   // Displaying a warning
   warning('You have been warned!');
   >>> Warning: You have been warned!
   
   // Pop a warning using the alertify framework
   throwWarning('You have been warned!');
 
``throwError()`` and ``error()`` functions:
***********************
 
Display an error alert message (in red) on the top of the model.
 
.. code-block:: javascript

   // Displaying an error
   error('Something went wrong! :(');
   >>> Error: Something went wrong! :(
   
   // Pop an error using the alertify framework
   throwError('Something went wrong! :(');

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

- ``static`` : We have a default value for the assumption (Example: GROWTH_YEARS: 5 - it will be 5 growth years by default)

- ``dynamic``: We can set the assumption by using ``setAssumption()`` (Example: _TREASURY_YIELD: '' - needs to be filled dynamically with the us 10 year treasury yield)

``Input()`` function:
*********************
  
The ``Input()`` function holds the interactive assumptions data, which the user is able tweak and play around with.

We usually use UPPERCASE when defining INPUT variables, so that we know it is referring to an assumption, but you can use whichever case you want.

The variable name will be formatted like so:

  ``NUMBER_ONE`` -> Number One
  
  ``Number_Two`` -> Number Two
  
  ``number_three`` -> Number Three

Use '_' as the first character when referring to a rate:

  ``_RATE: 10`` -> Will translate to 10% or 0.1
 
``setAssumption()`` and ``getAssumption()``:
********************************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L988>`__

Use ``setAssumption()`` to set a '' blank assumption dynamically and ``getAssumption()`` to retrieve the value of an assumption.

For example, if we wanted to set an assumption (``_TREASURY_YIELD``) to the Yield of the US 10 Year Treasury Bond. Assume we've got the treasury data in object ``treasury``.

  ``_TREASURY_YIELD: ''``
  
  ``setAssumption('_TREASURY_YIELD', treasury['year10']);``

Here is a code example of defining and setting assumptions:

.. code-block:: javascript

   Input(
      {
         NUMBER: 5,  // Static Assumption: Number 5
         CALCULATED_NUMBER: '',  // Dynamic Assumption (will be calculated later on)
         _RATE: 5,  // Static Assumption: Rate 5%
         _CALCULATED_RATE: '',  // Dynamic Assumption Rate (will be calculated later on)
      }
   ); 
   $.when().done(
      function(){
        // Set the dynamic assumption number
        setAssumption('CALCULATED_NUMBER', 1.23);

        // Set the dynamic assumption rate
        setAssumption('_CALCULATED_RATE', 1.23);
        
        print(getAssumption('NUMBER'), 'NUMBER');
        >>> NUMBER: 5 
        
        print(getAssumption('CALCULATED_NUMBER'), 'CALCULATED_NUMBER');
        >>> CALCULATED_NUMBER: 1.23 
        
        print(getAssumption('_RATE'), '_RATE');
        >>> _RATE: 0.05 
        
        print(getAssumption('_CALCULATED_RATE'), '_CALCULATED_RATE');
        >>> _CALCULATED_RATE: 0.0123 
   });

.. _render-chart-header-label:

Displaying a Chart - ``DateValueData.renderChart()``
----------------------------------------------------

Displays a chart based on a DateValueData object. 

If the DateValueData object has any editable keys, they will be displayed as editable chart points and placed inside the forecast table.

Arguments of ``DateValueData.renderChart(object)``

 * ``object`` - The object containing both the keys and the properties of the chart.
 
Example Usage
*************
 
.. code-block:: javascript

   // HISTORICAL_YEARS must be included
   Input(
     {			
       HISTORICAL_YEARS: 10,
     }
   );

   /*
         Format of object:
         object = {
            start_date: ...,  // Chart starts at start_date
            keys: ['key1', 'key2', ...],  // keys to be displayed on the chart (must be present in the DateValueData object)
            properties: {
               title: 'My Chart Title',  // The main title of the chart
               currency: ...,  // (Optional) In what currency are the chart's values
               number_format: 'M'/'K'/'',  // (Optional) 'M' for Millions, 'K' for thousands, blank for no number format
               disabled_keys: ['key1'],  // (Optional) keys that will be hidden by default, but can be toggled to visible from the chart
            }
         }
      */

   forecasted_data.renderChart({
     start_date: original_data.lastDate() - getAssumption('HISTORICAL_YEARS'),
     keys: ['revenue', 'netIncome', 'freeCashFlow', 'discountedFreeCashFlow'],
     properties: {
       title: 'Historical and forecasted data',
       currency: response.currency,
       number_format: 'M',
       disabled_keys: ['netIncome', 'discountedFreeCashFlow'],
     }
   });

Displaying a Table - ``DateValueData.renderTable()``:
-----------------------------------------------------

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/valuation-functions.js#L814>`__

Renders the table to the screen, similar to the ``DateValueData.renderChart()`` function.
 
Arguments of ``DateValueData.renderTable(object)``

 * ``object`` - The object containing both the keys and the properties of the table.

Example Usage
*************

.. code-block:: javascript

   // HISTORICAL_YEARS must be included
   Input(
     {			
       HISTORICAL_YEARS: 10,
     }
   );

   /*
         Format of object:
         object = {
            start_date: ...,
            keys: ['key1', 'key2', '_percentageKey', 'perShareKey', ...],
            rows: ['Key 1 Name', 'Key 2 Name', '{%} Rate Key Name', '{PerShare} Per Share Key Name', ...],
            'properties': {
               'title': 'My Table Title',  // Main title of the table
               'currency': ...,  // (Optional) In what currency are the table's values
               'number_format': 'M'/'K'/'',  // (Optional) 'M' for Millions, 'K' for thousands, blank for no number format
               'display_averages': true/false,  // (Optional) true for displaying an averages column
               'column_order': 'descending'/'ascending'  // (Optional) Sort the columns in 'ascending' order, or 'descending' order.
            }
         }
      */

   historical_computed_data.renderTable({
     start_date: original_data.lastDate() - getAssumption('HISTORICAL_YEARS'),
     keys: ['revenue', 'netIncome', 'totalStockholdersEquity', '_returnOnEquity', 'eps'],
     rows: ['Revenue', 'Net income', 'Total Equity', '{%} Return on equity', '{PerShare} EPS'],
     'properties': {
       'title': 'Historical Data',
       'currency': response.currency,
       'number_format': 'M',
       'display_averages': true,
       'column_order': 'descending'
     }
   });

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
   

``deepCopy()`` function:
************************

`Source <https://github.com/DiscountingCashFlows/Documentation/blob/632e8f8c894e7ac7b1c19e18c5fe6a1f69d85064/source-code/valuation-functions/new-valuation-functions.js#L337>`__

Creates a deep copy of the object that has been parsed and retrieves the underlying data.

In JavaScript, objects and arrays are mutable by default. Deep copying means cloning the original object into an identical copy, which you can modify without altering the original object.

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
 
