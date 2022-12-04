Using the Code Editor
====================

Make sure you have an active account, or make one to access the `Code Editor <https://discountingcashflows.com/valuation/>`__

.. _base-code:

Example Code for a Model
------------------------

Start a new model by going to the Code Editor where we will be writing our valuation model.

Copy the following base code into the editor:

.. code-block:: javascript

  var INPUT = Input({NUMBER: 5,
                     _RATE: ''});
  $.when(
    get_profile()).done(
    function(_profile){
      // We need to make sure we don't edit the _profile variable
      // Editing it could cause caching problems in the Watchlist page
      var profile = deepCopy(_profile);
      
      // Use console.log() to inspect the data
      // Press F12 or right-click to inspect the Console
      // You will see an exapandable object (profile)
      console.log(profile);

      // Print messages to the screen
      print("Hello World!", "First Message");
      print(profile.companyName, "Company's Name");
      print(profile.price, "Stock Price", "#", profile.currency);
      print(profile.website, "Company's Website");
  });

Running & Saving the Code
-------------------------

To run your code, you need to fill in a Ticker (Example: ``AAPL`` for Apple Inc.) and press Run. You can also name your model and save it.

This retrieves the company's profile data and prints out a ``Hello World`` message, the company's name, the last stock price and the company's website.

Press F12 or right-click and Inspect the page, and you will see an expandable object. This is the profile object that we have logged using ``console.log(profile);``.
If you expand it, you will see all the properties the object has. These properties are information about the company such as the company's full name (``profile.companyName``).

The following will be printed in the Terminal:

>>> First Message: Hello World! 
>>> Company's Name: Apple Inc. 
>>> Stock Price: 148.79 
>>> Company's Website: https://www.apple.com 


Base Code for a Model
---------------------

This is a starter preset with all basic functionalities of a model. Copy-Paste this into the valuation editor when creating a new model (it's easier than writing everything from scratch)

.. code-block:: javascript

  // +------------------------------------------------------------+
  //   Model Starter Preset
  //   © Copyright: https://discountingcashflows.com
  // +------------------------------------------------------------+

  var INPUT = Input({
                  NUMBER: '',
            _RATE: ''
              });

  $.when(
    get_income_statement(),
    get_profile(),
    get_treasury(),
    get_fx()).done(
    function(_income, _profile, _treasury, _fx){
      // Create deep copies of the response objects 
      // Editing the _objects directly can cause caching issues in Watchlist & Notifications
      var income = deepCopy(_income);
      // Company's Profile
      var profile = deepCopy(_profile);
      // Lastest Treasury Yields
      var treasury = deepCopy(_treasury);
      // Lastest FX Rates
      var fx = deepCopy(_fx);

      // +-------------------- ASSUMPTIONS SECTION -------------------+
      setInputDefault('NUMBER', 5);
      setInputDefault('_RATE', 1.23);
      // +---------------- END OF ASSUMPTIONS SECTION ----------------+

      // +---------------- VALUES OF INTEREST SECTION ----------------+
      // This estimated value of the stock
      var valuePerShare = 123;
      // Get the currency from the profile data
    var currency = profile.convertedCurrency;

      if(_StopIfWatch(valuePerShare, currency)){return;}

      print(profile.companyName, "Company's Name");
      print(1.23, 'Value', '#', currency);  // 1.23 USD
      print(1.23, 'Rate', '%');  // 123% 
      print(INPUT.NUMBER, 'INPUT.NUMBER', '#');
      print(INPUT._RATE, 'INPUT._RATE', '%');
      warning('You have been warned!');
      error('This is how you display an error!');

      // Print the value to the top of the model
      _SetEstimatedValue(valuePerShare, currency);
      // +------------- END OF VALUES OF INTEREST SECTION ------------+

      // +---------------------- CHARTS SECTION ----------------------+
      // Displaying a chart of Revenues
      // income.slice(0,10) gets the last 10 years of income statements
      // fillHistoricUsingReport() will fill in the revenues from the income statements
      // 'M' is for millions
      fillHistoricUsingReport(income.slice(0,10), 'revenue', 'M');

      // We can also make data forecasts using the forecast() function
      // Let's say revenues will grow 5% each year for 3 years
      var lastRevenue = toM(income[0].revenue);
      var forecastedRevenue = [lastRevenue * 1.05, 
                               lastRevenue * Math.pow(1.05, 2), 
                               lastRevenue * Math.pow(1.05, 3)];
      var forecastedRevenue = forecast(forecastedRevenue, 'revenue');
      // forecastedRevenue stores the value if the user modifies the forecast chart or table
      // use renderChart to display the chart to the screen
    renderChart('Revenues (In Mill. of ' + currency + ')');
      // +------------------- END OF CHARTS SECTION ------------------+

      // +---------------------- TABLES SECTION ----------------------+
      // Displaying tables
      var rows = ['Revenues', 'Net Income'];
      var columns = getYear(reportKeyToList(income, 'date'));
      var data = [reportKeyToList(income, 'revenue', 'M'), reportKeyToList(income, 'netIncome', 'M')];
      renderTable('Full history of data', data, rows, columns);
      // +------------------ END OF TABLES SECTION -------------------+
  });
  // Add a quick description shown at the top of the model 
  Description(`
    <h5>Model Starter Preset</h5>
    <p>This is a good starting point for writing new valuation models.</p>
    <p class='text-center'>Read more: <a href='https://github.com/DiscountingCashFlows/Documentation/' target='_blank'><i class="fab fa-github"></i> GitHub Documentation</a></p>
  `);
