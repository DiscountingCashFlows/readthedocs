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
      var profile = JSON.parse(JSON.stringify(_profile));
      profile = profile[0];
      
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

This is a preset with all basic functionalities of a model. Copy-Paste this into the valuation editor when creating a new model (it's easier than writing everything from scratch)

.. code-block:: javascript

  // +----------------------------------------------------------+
  //   Base Model Code
  //   Copyright: https://discountingcashflows.com, 2022			
  // +----------------------------------------------------------+

  var INPUT = Input({NUMBER: 5,
                    _RATE: ''}); 

  $.when(
    get_income_statement(),
    get_profile()).done(
    function(_income, _profile){
      // We need to make sure we don't edit the _profile variable
      // Editing it could cause caching problems in the Watchlist page
      var income = JSON.parse(JSON.stringify(_income));
      var profile = JSON.parse(JSON.stringify(_profile));
      profile = profile[0][0];
      income = income[0];
      // Use console.log() to inspect the data
      // Example: console.log(profile);

      // ---------------- ASSUMPTIONS SECTION ---------------- 
      // Set the INPUT._RATE to 1.23%
      setInputDefault('_RATE', 1.23);
      // ------------ END OF ASSUMPTIONS SECTION -------------

      // This valuePerShare and currency will be used as the intrinsic value of the stock
      var valuePerShare = 123;

      // Get the currency from the income statements 
    var currency = income[0].convertedCurrency;

      // ---------------- VALUES OF INTEREST SECTION ----------------
      // If we are calculating the value per share for a watch, we can stop right here
      if(_StopIfWatch(valuePerShare, currency)){
        return;
      }

      print('Hello World!');
      print(profile.companyName, "Company's Name");
      print('This is how you display an error', 'Error');
      print(1.23, 'Value', '#', currency);  // 1.23 USD
      print(1.23, 'Rate', '%');  // 123% 
      print(INPUT.NUMBER, 'INPUT.NUMBER', '#');
      print(INPUT._RATE, 'INPUT._RATE', '%');
      warning('You have been warned!');

      // Print the value to the top of the model
      _SetEstimatedValue(valuePerShare, currency);
    // ------------- END OF VALUES OF INTEREST SECTION ------------

      // ---------------- CHARTS SECTION ----------------
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
      // ------------- END OF CHARTS SECTION ------------

      // ---------------- TABLES SECTION ----------------
      // The context array is used to hold tables data
      var context = [];
      // Displaying tables
      var rows = ['Revenues', 'Net Income'];
      lastYearDate = parseInt(income[0]['date']);
      var columns = [];
      // Push all previous years until lastYearDate
      for(var i=1; i <= income.length; i++){
        columns.push(lastYearDate - i);
      }
      var data = [toList(income, 'revenue', 'M'), toList(income, 'netIncome', 'M')];
      context.push({name:'Full history of data', display:'table', rows:rows, columns:columns, data:data});
      // Render the table using monitor()
      monitor(context);
      // ------------ END OF TABLES SECTION ------------- 
  });

  // Add a quick description shown at the top of the model 
  var DESCRIPTION = Description(`<h5>Base Model Code</h5>
                                  <p>This is the base code for writing valuation models.</p>
                                  <p class='text-center'>Read more: <a href='https://github.com/DiscountingCashFlows/Documentation/' target='_blank'><i class="fab fa-github"></i> GitHub Documentation</a></p>
                                  `);
